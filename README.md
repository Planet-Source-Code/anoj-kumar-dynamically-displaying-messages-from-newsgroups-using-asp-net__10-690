<div align="center">

## Dynamically displaying messages from newsgroups using ASP\.Net


</div>

### Description

The code sample given below demonstrates how to communicate with a news server using NNTP.

The sample is not a complete implementation of NNTP client, but rather a demonstratio of how to communicate with a NNTP server via System.Net.Sockets.TcpClient . For more details on NNTP communication please refer RFC0977.
 
### More Info
 


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[Anoj Kumar](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/anoj-kumar.md)
**Level**          |Advanced
**User Rating**    |4.6 (23 globes from 5 users)
**Compatibility**  |VB\.NET, ASP\.NET
**Category**       |[Miscellaneous](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/miscellaneous__10-1.md)
**World**          |[\.Net \(C\#, VB\.net\)](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/net-c-vb-net.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/anoj-kumar-dynamically-displaying-messages-from-newsgroups-using-asp-net__10-690/archive/master.zip)





### Source Code

<table cellspacing="5" width="100%" border=1>
						<tr>
							<td class=normal>
								<div class=Heading><B>Dynamically displaying messages from newsgroups using ASP.Net</B></div><hr size=0><div><a target=_blank href='http://aspalliance.com/anoj/samples/newsreader.aspx'>See Online Demo</a></div><br>
		<p><b>Summary</b> <br>
 <br>
 The code sample given below demonstrates how to communicate with a news server
 using NNTP.</p>
<p>The sample is not a complete implementation of NNTP client, but rather a demonstratio
 of how to communicate with a NNTP server via
 <b>System.Net.Sockets.TcpClient </b>. For more details on NNTP communication
 please refer <b>RFC0977.</b></p>
<p><b>Code</b></p>
<div style="BACKGROUND-COLOR: moccasin">
<pre>
   &lt;%@ Page Language=&quot;VB&quot; Debug=&quot;true&quot; %&gt;
   &lt;%@ import Namespace=&quot;System.Net.Sockets&quot; %&gt;
   &lt;%@ import Namespace=&quot;System.IO&quot; %&gt;
   &lt;script runat=&quot;server&quot;&gt;
  Sub Page_Load(sender As Object, e As EventArgs)
   Dim tcpClient As New System.Net.Sockets.TcpClient()
   tcpClient.Connect(&quot;news.microsoft.com&quot;, 119)
   Dim networkStream As NetworkStream = tcpClient.GetStream()
   Dim responseArr() as string
   Dim bytes(tcpClient.ReceiveBufferSize) As Byte
   networkStream.Read(bytes, 0, CInt(tcpClient.ReceiveBufferSize))
   ' send GROUP command to get the staus and number of messages available
   ' the format returned will be
   '211 104 10011 10125 microsoft.public.dotnet.framework.aspnet group selected
   '(there are 104 articles on file, from 10011 to 10125)
   Dim sendBytes As [Byte]() = Encoding.ASCII.GetBytes(&quot;GROUP &quot; &amp; _
     DropDownList1.items(DropDownList1.selectedindex).value &amp; ControlChars.CrLf)
   networkStream.write(sendBytes,0,sendBytes.length)
   bytes.clear(bytes,0,bytes.length)
   networkStream.Read(bytes, 0, CInt(tcpClient.ReceiveBufferSize))
   responseArr=split(Encoding.ASCII.GetString(bytes))
   'we are interested in 3rd index as it contains the last number of message
   Dim fetchFrom as long=ctype(responseArr(3),long)
   fetchFrom =fetchFrom-6 ' subtract it from 10 as we r interested in latest 6 messages
   Dim i as integer
   for i=1 to 6
   sendbytes=Encoding.ASCII.GetBytes(&quot;ARTICLE &quot; &amp; fetchFrom &amp; ControlChars.CrLf )
   networkStream.write(sendBytes,0,sendBytes.length)
   bytes.clear(bytes,0,bytes.length)
   networkStream.Read(bytes, 0, CInt(tcpClient.ReceiveBufferSize))
   Dim Message as String
   Dim Header as String
   Dim Body as String
   Dim curLine as String
   Message=Encoding.ASCII.GetString(bytes)
   if left(Message,3)=&quot;220&quot; then ' if status is OK=220 then
	   Header=left(Message,instr(1,Message,ControlChars.CrLf &amp; ControlChars.CrLf))' get the header
  	 Body=mid(Message,instr(1,Message,ControlChars.CrLf &amp; ControlChars.CrLf))' get the body part
  	 Dim sr as new Stringreader(Header)
  	 curLine=sr.readLine()
  	 while not curLine is nothing
  	 	if instr(1,curLine,&quot;From:&quot;)=1 or instr(1,curLine,&quot;Sender:&quot;)=1 &amp; _
        or instr(1,curLine,&quot;Date:&quot;)=1 or instr(1,curLine,&quot;Message-ID:&quot;) then
  	  	AddToTable(curLine,true)
  	 	end if
  	 	curLine=sr.readLine()
  	 end while
  	 AddToTable(Body,false)
  else
  	 AddToTable(&quot;Failed to receive message (&quot; &amp; fetchfrom &amp;&quot;)&quot;,true)
  end if
   fetchfrom += 1
   next
   tcpClient.Close()
   End Sub
   Sub AddToTable(ss as String,Header as Boolean)
   Dim tr as new TableRow()
   Dim td as new TableCell()
   me.table1.rows.add(tr)
   tr.cells.add(td)
   if header then
   td.BackColor=System.Drawing.Color.Silver
   td.text=ss
   else
   td.text=&quot;&lt;PRE&gt;&quot; &amp; Server.HtmlEncode(ss) &amp; &quot;&lt;/PRE&gt;&quot;
   end if
   End SuB</pre>
 <pre>&lt;/script&gt;
   &lt;html&gt;
   &lt;head&gt;
   &lt;/head&gt;
   &lt;body&gt;
   &lt;form runat=&quot;server&quot;&gt;
   &lt;p&gt;
   &lt;table style=&quot;WIDTH: 508px; HEIGHT: 28px&quot; border=&quot;0&quot;&gt;
   &lt;tbody&gt;
   &lt;tr&gt;
   &lt;td&gt;
   Select a Newsgroup&lt;/td&gt;
   &lt;td&gt;
   &lt;asp:DropDownList id=&quot;DropDownList1&quot; runat=&quot;server&quot; Width=&quot;274px&quot;&gt;
   &lt;asp:ListItem Value=&quot;microsoft.public.dotnet.framework.aspnet&quot;&gt;
       microsoft.public.dotnet.framework.aspnet&lt;/asp:ListItem&gt;
   &lt;asp:ListItem Value=&quot;microsoft.public.dotnet.languages.vb&quot;&gt;
       microsoft.public.dotnet.languages.vb&lt;/asp:ListItem&gt;
   &lt;asp:ListItem Value=&quot;microsoft.public.xml&quot;&gt;microsoft.public.xml&lt;/asp:ListItem&gt;
   &lt;/asp:DropDownList&gt;
   &amp;nbsp;
   &lt;input type=&quot;submit&quot; value=&quot;Submit&quot; /&gt;&lt;/td&gt;
   &lt;/tr&gt;
   &lt;/tbody&gt;
   &lt;/table&gt;
   &lt;/p&gt;
   &lt;p&gt;
   &lt;br /&gt;
   &lt;asp:Label id=&quot;lab1&quot; runat=&quot;server&quot;&gt;&lt;/asp:Label&gt;
   &lt;br /&gt;
   &lt;asp:Table id=&quot;Table1&quot; runat=&quot;server&quot; BorderColor=&quot;Gray&quot; BorderWidth=&quot;1px&quot; CellSpacing=&quot;0&quot;
   CellPadding=&quot;3&quot; BorderStyle=&quot;Solid&quot; GridLines=&quot;Both&quot;&gt;&lt;/asp:Table&gt;
   &lt;/p&gt;
   &lt;/form&gt;
   &lt;/body&gt;
   &lt;/html&gt;
  </pre>
</div>

