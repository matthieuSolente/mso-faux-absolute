# mso-faux-absolute

Here is a way to create an **absolute positionning in Outlook without VML**.

For this to work, do not use tables inSIDE the absolute positionned block, they will be created automatically by the Outlook rendering engine. 
Don't wrap all the content of your email in a table, otherwise it won't work. 
You can use table Before or after the **MSO Faux absolute** bloc.

In our example, we'll use two separate elements: an image, which will act as a background image, and content. Each element is encapsulated in a div, to which we will add magic mso properties.

## Mso frames

For this to work in Outlook, we will use what word calls frames
mso-element:frame
As we can read in Stig's documentation
"Word allows the creation of floating frames in Web documents, even though there are currently no HTML elements to support this functionality"
From the moment we use properties related to frames, like the ones we will see just after, Outlook automatically inserts into the code, the mso-element:frame property. So we don't need to specify it in our template.

So here are the properties used to create absolute positioning in Outlook

### mso-element-frame-width 
This property will allow us to define the width of our frame. The same thing exists for the height, mso-element-height, but we do not need to specify it.
Using this property will automatically create a tabular structure in Outlook. So if we have the following structure:

```
<div align="center" style="mso-element-frame-width:500px">
	<p>Lorem ipsum dolor sit amet</p>
</div>
```

Outlook turns it into

```
<div style="mso-element:frame;mso-element-frame-width:375.0pt;mso-element-wrap:auto;mso-element-anchor-horizontal:column;mso-height-rule:exactly">
  <table>
    <tr>
      <td>
	      <p>Lorem ipsum dolor sit amet</p>
      </td>
    </tr>
  </table>
</div>
```

## The magic mso properties : mso-element-wrap & mso-element-left

Two properties used together will allow us to take an element out of the flow, and simulate an absolute positioning.
**mso-element-wrap** which takes the values :  around,auto,none,no-wrap-beside.
**mso-element-left** which takes the values : center,lenght, inside,left,outside,right.

### mso-element-wrap:none
To create a floating effect, two values work widht the mso-element-wrap property : **none** and **no-wrap-beside**.

With **mso-element-wrap:none** we already have an absolute positioning. So the following code works : As the text becomes floating, the image goes under the text.

```
<div style="mso-element-wrap:none;">
<h1>This is my Title</h1>
<p>Lorem ipsum dolor sit amet</p>
</div>
<img src="https://placehold.co/600x400" width="600" style="width:100%;max-width:600px;display:block;margin:0 auto;">
```

But the value 'none' has its limits. If you want to define a size to the frame with mso-element-frame-width or position it with mso-element-left, nothing works.

### mso-element-wrap:no-wrap-beside

If we want to position our frame with the mso-element-left property, only one value works, it is **no-wrap-beside**
So if we want to center our element, we add the two properties **mso-element-wrap:no-wrap-beside; mso-element-left:center**.
'Around','auto','none' values have no effect on positionning.

From the moment you use these two properties, the element takes an absolute positioning or the equivalent of float.

### mso-element-frame-hspace 

As we are in frames, none of the classic css properties work to center elements, so **mso-element-frame-hspace**  property can be used with pixel units to position an additional element differently;

## MSO Faux Absolulte example

To maintain a clean and consistent code, we will create our template by surrounding each element with a parent div.

So we will build our block with an image and then a text block that is supposed to be positioned on top. This is a basic example without any style, to improve for responsive etc. If you are testing on a Testi@ or EOA emulator, the default image will not be displayed everywhere as is often the case with placeholder images. Try with real images.

```
<!--- When using mso-elements you’ll see a dashed border appear around the first element used. Using a span with a 0px font size delete entirely this border; so this can be placed on top of the first element, or inside the first element -------------> 
<span style="mso-element-wrap:none;mso-element-left:center;font-size:0;">&zwnj;</span>

<div class="img" style="max-height:100px;mso-element-wrap:no-wrap-beside;mso-element-left:center;">
  <img src="https://placehold.co/600x400" width="600" style="width:100%;max-width:600px;display:block;margin:0 auto;">
</div>

<div style="mso-element-frame-width:400px;mso-element-wrap:no-wrap-beside; mso-element-left:center;mso-margin-top-alt:100px;max-width:400px;padding:20px;margin:0 auto">
    <h1>This is my Title</h1>
    <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam quis nostrud exercitation ullamco laboris nisi ut</p>
    <a href="htts://parcel.io" style="background-color:#005959; text-decoration: none; padding: .5em 2em; color: #FCFDFF; display:inline-block; border-radius:.4em; mso-padding-alt:0;text-underline-color:#005959">
    <!--[if mso]><i style="mso-font-width:200%;mso-text-raise:100%" hidden>&#8195;</i>
      <span style="mso-text-raise:50%;">
      <![endif]-->My link text<!--[if mso]>
      </span>
      <i style="mso-font-width:200%;" hidden>&#8195;&#8203;</i><![endif]-->
    </a>
</div>

<!---Adding a br clear=all to put back elements in the flow------------> 
<br clear="all"/>
```


Please test this code in Outlook, or try to play with the template mso-faux-absolute.html !
This small discovery would never have been possible without the detailed documentation of [Stig Morten Myre](https://stigmortenmyre.no/mso/html/concepts/ofconstyletable.htm) and [Mark Robbins](https://www.goodemailcode.com/email-enhancements/mso-styles)
Created by Matthieu Solente in May 2023

