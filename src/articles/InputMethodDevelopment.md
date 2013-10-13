# 输入法相关程序的开发

为了支持 Haiku，并且为它做些有意义的事，我非常愿意为您展示与 BeOS 的 input server 进行交互的方法。

在 BeOS 中，基本上所有内容都使用 UTF-8 编码类型进行字符的处理。因此，如果您希望是用编辑和显示英语以外的其他语言字符，所有您需要做的就是寻找可以显示该类字符的可用的字体，以及用于向正在运行的程序输出字符的输入法加载项。BeOS 的输入法最初源于日语支持。它以如下方式工作：input server 加载位于 B_SYSTEM_ADDONS_DIRECTORY /input_server/methods 或者 B_USER_ADDONS_DIRECTORY/input_server/methods 的加载项，然后利用 BinputServerFilter 提供的 Filter() 方法修改并过滤由键盘或者鼠标产生的事件。从BeOS R4 发布至今，仅完成了很少几个输入法。这可能是因为仅有 ERGOSOFT Crop. 和极少的几个开发者了解输入法如何进行工作。在我的记忆中，现存的输入法有 BeCJK，HanBe，Canna，ChineseTool 以及最近的 “Anthy for Zeta”。

可能您不是很关心输入法的开发，但是当实现 BView 的派生类时，您可能需要了解这方面的内容。您创建的类需要处理国际化的文本，例如文档编辑器或者网页处理等。尽管 BTextView 类可以处理大部分的工作。BTextView 是一个非常大的类，有些时候为了某些目的，您可能需要编写一些定制化和更加灵活的类。

那么接下来我们分两部分来讲解。第一部分将会向你介绍如何编写一个输入相关的程序，而第二部分我们将会介绍如何编写一个输入法。

## 第一部分：输入法相关的 BView 派生类

本指南基于我很早以前完成的一个类，它的头文件内容如下：

<pre>
#ifndef __SINPUT_AWARE_STRING_VIEW_H__
#define __SINPUT_AWARE_STRING_VIEW_H__
 
#include <SupportDefs.h>
#include <Messenger.h>
#include <Input.h>
 
#include <besavager/StringView.h>
 
 
class SInputAwareStringView : public SStringView {
public:
	SInputAwareStringView(BRect frame, const char *name,
						  uint32 resizeMask = B_FOLLOW_LEFT | B_FOLLOW_TOP,
						  uint32 flags = B_WILL_DRAW | B_FRAME_EVENTS);

	virtual void MessageReceived(BMessage *message);
 
private:
	BMessenger msgr_input;
	bool im_started;

	void IMStarted(BMessage *message);
	void IMStopped();
	void IMChanged(BMessage *message);
	void IMLocationRequest(BMessage *message);
};
 
#endif /* __SINPUT_AWARE_STRING_VIEW_H__ */
</pre>

### Section 1.1 B_INPUT_METHOD_AWARE 标志

如果Bview标志包含B_INPUT_METHOD_AWARE，那么就意味着由输入法创建的B_INPUT_METHOD_EVENT消息将会被MessageReceived()方法所处理，否则input server将会弹出处理该消息的窗口，然后发送消息（B_KEY_DOWN）给相应的程序（但在新版本的BeOS，如Dano/Zeta等中将会导致奇怪的错误）。

因此，该派生类的构造函数应该完成如下的操作：

<pre>
SInputAwareStringView::SInputAwareStringView(BRect frame, const char *name, uint32 resizeMask, uint32 flags)
        : SStringView(frame, name, NULL, NULL, 0, NULL, resizeMask, flags | B_INPUT_METHOD_AWARE), im_started(false)
{
}
</pre>

### Section 1.2 B_KEY_DOWN 消息

B_KEY_DOWN消息指最重要的键盘事件。当该消息的标志中不包含B_INPUT_METHIOD_AWARE时，input server 将发送该消息给正在运行的程序的活跃视图。

该消息包含的字段如下表所示(来源于 Be Book)

<table border="1">
<tr> <td>字段	    </td><td>类型代码	    </td><td>描述</td> </tr>
<tr> <td>"when"	    </td><td>B_INT64_TYPE	</td><td>事件时间，从01/01/70起始，以微秒来计数。</td> </tr>
<tr> <td>"key"	    </td><td>B_INT32_TYPE	</td><td>按下的物理按键的编码，您可以通过下面讲述的"key"的函数来获取按键编码。</td></tr>
<tr> <td>"be:key_repeat" </td><td>B_INT32_TYPE	</td><td> 按键的“迭代次数”</td> </tr>
<tr> <td>"modifiers"</td><td>B_INT32_TYPE	</td><td> 事件发生期间，有效的修饰键。</td> </tr>
<tr> <td>"states"	</td><td>B_UINT8_TYPE	</td><td> 事件发生期间，所有按键的状态。</td> </tr>
<tr> <td>"byte"[3] 	</td><td>B_INT8_TYPE	</td><td> 产生的UTF-8数据，它包含3个字节的信息。</td> </tr>
<tr> <td>"bytes"	</td><td>B_STRING_TYPE	</td><td> 产生的UTF-8字符串。</td> </tr>
<tr> <td>"raw char"	</td><td>B_INT32_TYPE	</td><td> 用于表示字符的独立于修饰键之外的ASCII编码。</td> </tr>
</table>

一个完美的派生类的重点首先应该放在 "bytes" 字段的内容，如果 "bytes" 不存在，则是 `"byte"[3]` 字段，最后才是 "raw_char" 字段。

### Section 1.3 B_INPUT_METHOD_EVENT 消息

当输入法开始工作时，input server 发送该消息给相应的视图。如果师徒的标志未包含 B_INPUT_METHOD_AWARE，那么该消息将会被忽略。下面的段落引用自 Be Book。

每个B_INPUT_METHOD_EVENT消息都包含了一个表明事件类型的be:opcode字段(一个int32类型数值)：

<table border="1">
<tr> <td>数值	                         	</td><td>描述</td> </tr>
<tr> <td>B_INPUT_METHOD_STARTED	         	</td><td>表明新输入处理的开始</td> </tr>
<tr> <td>B_INPUT_METHOD_STOPPED	         	</td><td>表明输入处理的结束</td> </tr>
<tr> <td>B_INPUT_METHOD_CHANGED	         	</td><td>表明处理状态的改变</td> </tr>
<tr> <td>B_INPUT_METHOD_LOCATION_REQUEST	</td><td>表明输入法询问每个字符在屏幕中的位置。</td> </tr>
</table>

补充，除了B_INPUT_METHOD_STOPPED，这一些特别的字段还包含于其他的数值。

B_INPUT_METHOD_STARTED包含了以下的字段：

<table border="1">
<tr> <td>字段	        </td><td>类型代码	      </td><td>描述</td> </tr>
<tr> <td>"be:reply_to"	</td><td>B_MESSENGER_TYPE </td><td>在处理过程中于您进行交流的信使</td> </tr>
</table>

由"be:reply_to" 所指向的信使通常用于反馈字符位置给输入法，您也可以使用它发送B_INPUT_METHOD_STOPPED类消息或者其他消息停止处理过程。

B_INPUT_METHOD_CHANGED包含了一下字段：

<table border="1">
<tr> <td>字段	            </td><td>类型代码	   </td><td>描述</td> </tr>
<tr> <td>"be:string"	    </td><td>B_STRING_TYPE </td><td>当前用户输入的文本；接收器通常会在当前插入点显示它们。BtextView 也会对它进行蓝色高亮显示，以表明它们属于临时处理的部分。</td> </tr>
<tr> <td>"be:selection"  	</td><td>B_INT32_TYPE  </td><td> 一对be:string内部的字节类型B_INT32_TYPE偏移量，当部分的be:string当前被选中。BtextView将会对选中部分红色高亮显示以区别于蓝色。</td> </tr>
<tr> <td>"be:clause_start"	</td><td>B_INT32_TYPE  </td><td> Be:string内部用于处理语言(例如汉语)的0或者更大偏移量，它们把句子或短语分成许多子句。等数量成对的be:clause_start和be:clause_end用于划定子句的边界；在子句边界处，BtextView将会分开蓝/红高亮显示。</td> </tr>
<tr> <td>"be:clause_end" 	</td><td>B_INT32_TYPE  </td><td> be:string 内部的 0 或者更大的偏移量；而且 be:clause_end 和 be:clause_start 数量必须相等。</td> </tr>
<tr> <td>"be:confirmed"  	</td><td>B_BOOL_TYPE	</td><td> 当用户完成了输入，并且“确认”了当前的字符串，希望结束处理时，该字段为 "True"。BtextView 将不高亮显示文本，而是等待 B_INPUT_METHOD_STOPPED (关闭处理)或者 B_INPUT_METHOD_CHANGED (及时启动新的处理)。</td> </tr>
</table>

当B_INPUT_METHOD_EVENT类型是B_INPUT_METHOD_LOCATION_REQUEST是，派生类将会把包含以下字段的消息反馈给B_INPUT_METHOD_STARTED类型中的"be:reply_to"字段所指向的信使：

<table border="1">
<tr> <td>字段	                </td><td>类型代码		</td><td>描述</td> </tr>
<tr> <td>"be:opcode"	        </td><td>B_INT32_TYPE	</td><td>必须设置为 B_INPUT_METHOD_LOCATION_REQUEST </td> </tr>
<tr> <td>"be:location_reply"	</td><td>B_POINT_TYPE	</td><td>每个 UTF-8 字符在屏幕中的显示坐标。</td> </tr>
<tr> <td>"be:height_reply"		</td><td>B_FLOAT_TYPE	</td><td>在 be:string 中每个字符(可能包含多个字节)的高度。</td> </tr>
</table>

### Section 1.4 示例

那么现在开始，如果您已经转备好了编码，我们就开始进行练习。

<pre>
void
SInputAwareStringView::MessageReceived(BMessage *message)
{
	switch(message->what)
	{
			case B_INPUT_METHOD_EVENT: // input method event received
					{
							int32 op_code; // the kind of message
							if(message->FindInt32("be:opcode", &op_code) != B_OK) break;

							switch(op_code)
							{
									case B_INPUT_METHOD_STARTED: // prepare for input transaction
											IMStarted(message);
											break;

									case B_INPUT_METHOD_STOPPED: // stop the transaction and clear something
											IMStopped();
											break;

									case B_INPUT_METHOD_CHANGED: // displaying characters when the string entering changed
											IMChanged(message);
											break;

									case B_INPUT_METHOD_LOCATION_REQUEST: // reply the lcoation of characters
											IMLocationRequest(message);
											break;

									default: // call member function of base class
											SStringView::MessageReceived(message);
							}
					}
					break;

			default: // call member function of base class
					SStringView::MessageReceived(message);
	}
}


void
SInputAwareStringView::IMStarted(BMessage *message)
{
	// here we store the messenger that we would communicate with the input method during the transaction
	im_started = (message->FindMessenger("be:reply_to", &msgr_input) == B_OK);
}


void
SInputAwareStringView::IMStopped()
{
	msgr_input = BMessenger();
	SetText(NULL); // clear the text had shown
	im_started = false;
}


void
SInputAwareStringView::IMChanged(BMessage *message)
{
	if(!im_started) return;

	const char *im_string = NULL;
	message->FindString("be:string", &im_string);
	if(im_string == NULL) im_string = "";

	int32 start = 0, end = 0;

	BList color_list;

	for(int32 i = 0;
			message->FindInt32("be:clause_start", i, &start) == B_OK &&
			message->FindInt32("be:clause_end", i, &end) == B_OK; i++)
	{
			// handle clauses
			if(end > start) // visible
			{
					// set the background of clauses to be blue, the offsets are in bytes.
					// for example, the "[/]" means the start/end offset of that.
					// string: T h i s i s a [w o r d] .
					// offset: 0 1 2 3 4 5 6 7  8 9 10 11
					// "be:clause_start" = 7
					// "be:clause_end" = 11
					s_string_view_color *color = new s_string_view_color;
					s_rgb_color_setto(&color->color, 0, 0, 0);
					s_rgb_color_setto(&color->background, 152, 203, 255);
					color->draw_background = true;
					color->start_offset = start;
					color->end_offset = end - 1;
					color_list.AddItem((void*)color);
			}
	}

	for(int32 i = 0;
			message->FindInt32("be:selection", i * 2, &start) == B_OK &&
			message->FindInt32("be:selection", i * 2 + 1, &end) == B_OK; i++)
	{
			// handle selection
			if(end > start) // visible
			{
					// set the background of clauses to be red, the offsets are in bytes.
					// for example, the "[/]" means the start/end offset of that.
					// string: T h i s i s a [w o r d] .
					// offset: 0 1 2 3 4 5 6 7  8 9 10 11
					// "be:selection"[0] = 7
					// "be:selection"[1] = 11
					s_string_view_color *color = new s_string_view_color;
					s_rgb_color_setto(&color->color, 0, 0, 0);
					s_rgb_color_setto(&color->background, 255, 152, 152);
					color->draw_background = true;
					color->start_offset = start;
					color->end_offset = end - 1;
					color_list.AddItem((void*)color);
			}
	}

	if(!color_list.IsEmpty()) // you don't need to know what it is below
	{
			int32 n = color_list.CountItems();
			s_string_view_color *colors = new s_string_view_color[n];

			for(int32 i = 0; i < n; i++)
			{
					s_string_view_color *color = (s_string_view_color*)color_list.ItemAt(i);
					if(color)
					{
							if(colors)
							{
									s_rgb_color_setto(&colors[i].color, color->color);
									s_rgb_color_setto(&colors[i].background, color->background);
									colors[i].draw_background = color->draw_background;
									colors[i].start_offset = color->start_offset;
									colors[i].end_offset = color->end_offset;
							}

							delete color;
					}
			}

			color_list.MakeEmpty();

			SetText(im_string, colors, n);
			if(colors) delete[] colors;
	}
	else
	{
			SetText(im_string);
	}
}


void
SInputAwareStringView::IMLocationRequest(BMessage *message)
{
	if(!im_started) return;

	const char *im_string = Text();
	if(im_string == NULL || *im_string == 0) return;

	BMessage reply(B_INPUT_METHOD_EVENT); // the message for reply
	reply.AddInt32("be:opcode", B_INPUT_METHOD_LOCATION_REQUEST); // must set this

	BPoint left_top = ConvertToScreen(BPoint(0, 0)); // first we convert the (0, 0) to the coordinate relative to the display

	int32 offset = 0;
	uint32 index = 0;

	while(true)
	{
			if(__utf8_char_at(im_string, index, &offset)) // the (index + 1)th UTF-8 character
			{
					BRect rect = TextRegion(offset, offset + 1).Frame(); // get the region of text relative to the view
					if(!rect.IsValid()) break;

					// convert to the coordinate relative to the display
					BPoint pt = left_top;
					pt += rect.LeftTop();

					reply.AddPoint("be:location_reply", pt); // the left-top point of each character
					reply.AddFloat("be:height_reply", rect.Height()); // the height of each character
			}
			else
			{
					break;
			}

			index++;
	}

	// send the message to the input method within input server
	msgr_input.SendMessage(&reply);
}
</pre>













