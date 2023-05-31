# MSO Faux Absolute Positioning for Overlapping Email Elements in Outlook

Here is a way to create an **absolute positionning in Outlook without VML**.
See a basic example on my [codePen](https://codepen.io/matthieuSolente/pen/ZEqXgPL) 
See also a typical design that we meet a lot in emails on [this codepen](https://codepen.io/matthieuSolente/pen/abRVodY)

For this to work, don't wrap all the content of your email in a table :According to my tests in any case, encapsulating the mso block in a table container can be problematic and break the desired effect.Inside the block, you can use tables sparingly to perfect the rendering on Outlook (add padding etc.) 
You can also use table Before or after the **MSO Faux absolute** block.

There seems to be a z-index issue on Windows 10 and 11, with the text going under the image. A resolution is in progress, but this bug is no longer present in the New Outlook of these two versions.

## The scenario

In our example, we'll use two separate elements: an image, which will act as a background image, and content. Each element is encapsulated in a div, to which we will add magic mso properties.

![image](https://user-images.githubusercontent.com/7109121/235813558-dab71670-a14c-4c58-96ac-c2cf54af90ce.png)

## Mso frames

For this to work in Outlook, we will use what word calls frames.

**mso-element:frame**

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
To create a floating effect, two values work with the mso-element-wrap property : **none** and **no-wrap-beside**.

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
'Around','auto','none' values have no effect on positioning.

From the moment you use these two properties, the element takes an absolute positioning or the equivalent of float.

### mso-element-frame-hspace 

As we are in frames, none of the classic css properties work to center elements, so **mso-element-frame-hspace**  property can be used with pixel units to position an additional element differently;

## MSO Faux Absolute example

To maintain a clean and consistent code, we will create our template by surrounding each element with a parent div.

So we will build our block with an image and then a text block that is supposed to be positioned on top. This is a basic example without a minimum style, which will have to be adapted for each particular case.

When you use mso-elements, you may see a white dotted border around the first element used. In this case, placing this span makes it possible to completely reduce this border. It can be placed above the first element, inside, just after the first div, or alternatively, at the highest level of the email, to be sure that any margin does not alter your design:

''' <span style="mso-element-wrap:none;mso-element-left:center;font-size:0"></span> '''

```
 <span style="mso-element-wrap:none;mso-element-left:center;font-size:0;"></span>      
<div style="max-height:100px;mso-element-wrap:no-wrap-beside;mso-element-left:center;">
  <img src="" alt="">
</div>

    <div style="mso-element-frame-width:400px;mso-element-wrap:no-wrap-beside; mso-element-left:center;mso-margin-top-alt:100px;">          
      <h1>Lorem</h1>
      <p>Lorem ipsum dolor sit amet
      </p>
      <a href="https://emailresourc.es/" style="background-color:#005959;text-decoration: none; padding: .5em 2em; color: #FCFDFF; display:inline-block; border-radius:.4em; mso-padding-alt:0;text-underline-color:#005959">
    <!--[if mso]><i style="mso-font-width:200%;mso-text-raise:100%" hidden>&#8195;</i><span style="mso-text-raise:50%;"><![endif]-->Email Resources<!--[if mso]></span><i style="mso-font-width:200%;" hidden>&#8195;&#8203;</i><![endif]-->
      </a>
    </div>


<!---If you perceive alignment bugs before and after your mso-element, you can try to restore the flow with a <br clear="all"/>------------> 
<br clear="all"/>
```
You can achieve the same result by using a table too

```
<div style="max-height:100px;mso-element-wrap:no-wrap-beside;mso-element-left:center;">
  <img src="" alt="">
</div>
<table role="presentation" width="100%" cellspacing="0" cellpadding="0">
  <tr>
    <td style="mso-element-frame-width:400px;mso-element-wrap:no-wrap-beside; mso-element-left:center;mso-margin-top-alt:100px;">          
      <h1>Lorem</h1>
      <p>Lorem ipsum dolor sit amet
      </p>
      <a href="https://emailresourc.es/" style="background-color:#005959;text-decoration: none; padding: .5em 2em; color: #FCFDFF; display:inline-block; border-radius:.4em; mso-padding-alt:0;text-underline-color:#005959">
    <!--[if mso]><i style="mso-font-width:200%;mso-text-raise:100%" hidden>&#8195;</i><span style="mso-text-raise:50%;"><![endif]-->Email Resources<!--[if mso]></span><i style="mso-font-width:200%;" hidden>&#8195;&#8203;</i><![endif]-->
      </a>
    </td>
  </tr>
</table>
```
Another alternative is to use a main container, a div, to which we add the two properties mso-element-wrap:no-wrap-beside;mso-element-left:center; Inside it we place our image and our text. Note that for the text to float above the image, the mso-element-frame-width property is required.

```
<span style="mso-element-wrap:none;mso-element-left:center;font-size:0;"></span>   
<div style="mso-element-wrap:no-wrap-beside;mso-element-left:center;">   
  <div style="max-height:100px;">
    <img class="mso-fauxAbsolute__img" src="https://picsum.photos/600/400?random=1" width="600" border="0" style="width:100%;max-width:600px;display:block;border:0;margin:0 auto;">
  </div>
  <div style="mso-element-frame-width:400px;mso-margin-top-alt:100px;max-width:400px;">          
    <h1>This is my Title</h1>
    <p>Lorem ipsum dolor sit amet</p>
    <a href="https://emailresourc.es/" style="background-color:#005959; text-decoration: none; padding: .5em 2em; color: #FCFDFF; display:inline-block; border-radius:.4em; mso-padding-alt:0;text-underline-color:#005959">
	<!--[if mso]><i style="mso-font-width:200%;mso-text-raise:100%" hidden>&#8195;</i>
	<span style="mso-text-raise:50%;">
	  <![endif]-->Email Ressourcest<!--[if mso]>
	</span>
	<i style="mso-font-width:200%;" hidden>&#8195;&#8203;</i><![endif]-->
      </a>     
  </div>
</div>
```
	
Please test this code in Outlook, Do not hesitate to consult my example template (mso-faux-absolute.html) for a slightly more optimized version !

## Accessibility 

In terms of accessibility, the title and button are accessible and interpreted as is. NVDA reports the presence of the level 1 title as well as the link. From the top of the email you can reach the title with the shortcut "H" and the link with the shortcut "K", or with the tab key.

## Windows 10 & 11 

On these two mailboxes, the text remains under the image and is therefore hidden. One solution is to code the thing differently, using mso-element-wrap:none.

```
<div style="mso-element-frame-width:400px;mso-element-wrap:none; mso-element-left:center;mso-margin-top-alt:100px;">          
  <h1>Lorem</h1>
  <p>Lorem ipsum dolor sit amet
  </p>
  <a href="https://emailresourc.es/" style="background-color:#005959;text-decoration: none; padding: .5em 2em; color: #FCFDFF; display:inline-block; border-radius:.4em; mso-padding-alt:0;text-underline-color:#005959">
    <!--[if mso]><i style="mso-font-width:200%;mso-text-raise:100%" hidden>&#8195;</i><span style="mso-text-raise:50%;"><![endif]-->Email Resources<!--[if mso]></span><i style="mso-font-width:200%;" hidden>&#8195;&#8203;</i><![endif]-->
  </a>        
</div>
<img src="" alt="">
```
This will also cancel the absolute positioning on all other customers. It will therefore be necessary to rethink your code to make it work everywhere. This bug only occurs on these two mailboxes and Wall! Email. With the new Outlook on Windows Mail, this problem disappears. I am working to solve this problem, but if a fix is found, it will only be short-lived;
Here, experimentation is necessary as much to make floating positioning work on Windows 10 & 11Mail, as well as to create other types of layout with these properties

## Z-index 

If you have problems with z-index or elements not displaying on the web version (borders o rbackground color for example), try adding a display:inline-block on the div concerned, (some time position:relative can also work). 
Here you can see an example where without the display:inline-block property, the background color of the text block disappears : https://codepen.io/matthieuSolente/pen/abRVodY
This same technique also solves this kind of problem on the classic absolute positioning in vml( test [this codepen](https://codepen.io/matthieuSolente/pen/QWMLeZW))

## CTA 

If you use a classic table-coded CTA link, it can work. Otherwise Mark Robbins' html button is an ideal solution in this case. Feel free to use my [generator](https://codepen.io/matthieuSolente/pen/abRpPOX) to create your link

This small discovery would never have been possible without the detailed documentation of [Stig Morten Myre](https://stigmortenmyre.no/mso/html/concepts/ofconstyletable.htm) and [Mark Robbins](https://www.goodemailcode.com/email-enhancements/mso-styles)
Created by Matthieu Solente in May 2023

