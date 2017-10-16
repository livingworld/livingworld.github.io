---
layout: post
title: "asp：button和input的区别"
date: 2017-04-09  12:00:00 +0800
description: "asp：button和input的区别"
categories: c#
tag: [asp.net]
---   

## input runat= server和asp:button的区别?
- 一个是`<input type= "button" runat= "server "/>`，对应System.Web.UI.HtmlControls.HtmlInputButton类 
- 一个是`<asp:button runat= "server "> </asp:button>`对应System.Web.UI.WebControls.Button类

## answer:

>One is a server control (the asp button) that when rendered on the page includes javascript that handles the postback logic, as well as being exposed to your code-behinds as a control you can address by its ID. The Html control is platform agnostic, and is rendered by your browser as just a button. That button will raise click events but will not POST your form.

## Ask:Difference betwen button and asp:button onclick

- Code 1

  - aspx code

    <asp:Button ID="Button1" runat="server" onclick="Button1_Click" Text="Button" />  
    Codebehind

    protected void Button1_Click(object sender, EventArgs e)
    {
    //Do somthing
    } 

- Code 2

    - aspx code

    <input id="Submit1" type="submit" value="submit" onclick="return Submit1_onclick()" />  


    <script language="javascript" type="text/javascript">
    // <![CDATA[

    function Submit1_onclick() {
    //Do somthing
    }

    // ]]>
    </script>

### answer:

answer by Felipe Oriani:

> The first code it is a server side code. When you add a tag asp:button in your webform, the asp.net will render an input type="button" for you and when you click this button, it will submit a post to the same page (this is called postback) and will processing the life cycle and asp.net event associated with this button. The same is valid for every server control such as TextBoxes (render input type='text'), Panels (renders divs), Checkboxes (render input type='checkbox'), Labels (render spans) etc...

>In the second code, you have the code that first one will render (an input type = 'button'), but associated with an event in the client-side javascript.

>You can also associate a client-side event in a tag asp:button, take a look at the OnClientClick property.

answer by Oded:

>The first is a server side control and the event handler is executed on the server in C#. Clicking the button will cause a postback and all information in the form will be posted to the server for processing, including a call to the click event handler.

>The second is fully client side and the event handler is executed in the browser in JavaScript.