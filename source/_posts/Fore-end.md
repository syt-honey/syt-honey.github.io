---
title: Record of Fore-end
tags: js
---

#### HTML5
---

1. external link:
```html
  <!--href: URL address-->
  <!--target:where to open the link-->
  <!--eg:"_blank"specifies to open the link in a new tab.-->
  <a href="http://freecatphotoapp.com" target="_blank">cat photos</a>
```
2. internal link: 
```html
  <a href="#contacts-header">Contacts</a>
  <a id="contacts-header">Contacts</a>
```
3. dead link:hash symbol ,to create a dead link.
   eg:`href="#"`
4. let the picture to a link:
```html
  <a href="#"><img src="https://bit.ly/fcc-relaxing-cat" alt="A cute orange cat lying on its back."></a>
```
5. Create a Form Element:
```html
  <form action="/url-where-you-want-to-submit-form-data"></form>
```
6. Create a placeholder in the input field.
```html
<input type="text" placeholder="cat..." required>
```
7. Create a Set of Radio Buttons
```html
  <label>
    <input type="radio" name="indoor-outdoor">Indoor
  </label>
```

#### Basic CSS
---

1. Basic CSS:Change the Color of Text
```html
  <h2 style="color: blue;">CatPhotoApp</h2>
```
2. Import a Google Font
```html
  <link href="https://fonts.googleapis.com/css?family=Lobster" rel="stylesheet" type="text/css">
```
3. border-radius: 50%
4. Use the `[type = 'radio']` to set the property.
```css
  [type='radio'] {
    margin: 20px 0px 20px 0px;
  }
```
5. Use a custom CSS Variable
```css
  --penguin-skin: gray;
  background: var(--penguin-skin);
```

#### Applied Visual Design
---

1. add a box-shadow to a Card-like Element

>The `box-shadow` property applies one or more shadows to an element.

>The `box-shadow` property takes values for `offset-x` (how far to push the shadow horizontally from the element), `offset-y` (how far to push the shadow vertically from the element), `blur-radius`, `spread-radius` and a color value, in that order. The `blur-radius` and `spread-radius` values are optional.

>Here's an example of the CSS to create multiple shadows with some blur, at mostly-transparent black colors:
```css
   box-shadow: 0 10px 20px rgba(0,0,0,0.19), 0 6px 6px rgba(0,0,0,0.23);
```
2. Create a Gradual CSS Linear Gradient
```css
  background: linear-gradient(gradient_direction, color1, color2,...);
  background: linear-gradient(35deg, #CCFFF, #FFCCCC);
    
  div{ 
    border-radius: 20px;
    width: 70%;
    height: 400px;
    margin: 50px auto;
    background: linear-gradient(35deg, #CCFFFF, #FFCCCC);
  }
```
3. Use the css transform scale property to scale an element on hover
```css
  <style>
    div {
      width: 70%;
      height: 100px;
      margin: 50px auto;
      background: linear-gradient(53deg, #ccfffc, #ffcccf);
    }
    div:hover {
      transform: scale(1.1);
    }
  </style>
```
4. Learn how the css `@keyframes` and animation properties work
    * `animation-name`:sets the name of the animation,which is later used by `@keyframes`to tell CSS which rules go with which animations.
    * `animation-duration`:sets the length of time for the animation. 
    * `animation-iteration-count`:the count of animation will stop after running, but it's possible to make the animation run continuously by setting that value to infinite.
    * `animation-timing-function`:controls how quickly an animates element changes over the duration of the animation.
```css
  <style>
    div {
      height: 40px;
      width: 70%;
      background: black;
      margin: 50px auto;
      border-radius: 5px;
    }
    #rect {
      animation-name: rainbow;
      animation-duration: 4s;
    }
    @keyframes rainbow {
      0% {
        background-color: blue;
      }
      50% {
        background-color: green;
      }
      100% {
       background-color: yellow;
      }
    }
  </style>
```

有问题？发送 [issues](https://syt-honey.github.io/about/) 给我~
