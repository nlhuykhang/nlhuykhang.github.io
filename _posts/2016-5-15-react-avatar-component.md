---
layout: post
title: React - Build an Avatar Component by SVG which support images, initials, and icon font
categories: [javascript, framework]
tags: [study, tech, react, svg]
published: true
fullview: true
---

Warning: this post is written with my poor english

# Introduction

Have you ever used Google Docs and had a liking for its anonymous avatar (avatar with random animals) or the auto-generated initials avatar for users do not have avatars?

FYI I have, not only that I also really fancy those auto-generated avatar, and want to implement something like that to my project. After some time of research, I did and this blog is about how I implement that avatar system using React.

# Requirements

Below are the requirements for the avatar component:

- Be a dumb component, simply renders avatar depend on its props
- Support images loaded by url
- Support initials avatar
- Support icon font avatar, e.g. use the icon loaded in font to be the avatar
- Should not depend on external libraries/plugins
- Simple

There is a good Jquery plugin named [initials.js](http://judelicio.us/initial.js/) which help generate the avatar. In the React world, however, it is not a qualified solution because I do not want to call this plugin in every componentDidMount hook for every avatar on the screen. It also does not have with icon font and images loaded by url.

There are also some other React avatar component but none of them matches my requirements.

The hardest part is how to created initials/icon font avatar when using minimal amount of external libraries. Because it would create a lot of complexity to integrate many plugins (properly Jquery plugins) and raise a question about performance.

I was looking for something simple, fast, and "native" to the browser.
Luckily, I found my answer.

# SVG is the solution
Look at many examples on the internet. I realized SVG is the best solution. Because:

- It does not require any external libraries/plugins to use SVG
- SVG is scalable in its nature
- Inside the SVG tag, there is a tag named text which displays plain text or font icon by icons' string character
- It costs no time to generate the avatar image (because nothing to generate, just need to render)

This is example of the SVG tag to display avatar with initials:

    <svg height="24" width="24">
      <rect fill="#a0d36a" x="0" y="0" height="24" width="24"></rect>
      <text
        fill="#ffffff"
        font-size="12"
        text-anchor="middle"
        x="12"
        y="16">KH</text>
    </svg>

And result is like this:
![SVG initials avatar]({{ site.url }}/assets/media/2016-5-15-react-avatar-component/1.png)

That looks pretty, but how about the font icon avatar?
It is simple too though it requires some prerequisites first. That said because I use the icon load from font so first that font file need to be loaded to the browser. Second you need to have the string character of the icon be displayed.
To have these values, let do some little tricks:

- Use the [Icomoon app](https://icomoon.io/app/#/select) to generate the font files
- Apart from the font files we also get a demo html file which displays the icons and their string values (the square symbols beneath each icons)
- Now you just need to write a simple JQuery scirpt to get those values in a format that suite your use-case. (In my case, I composed them to a single string array cause I just want to generate icon automatically, a list value is the best solution)

Now having the font files loaded and the list of icon values, you just need to randomly get a value from the list and put it inside text tag of the svg tag. Like this:

    <svg height="64" width="64">
      <rect fill="#a0d36a" x="0" y="0" height="64" width="64"></rect>
      <text class="avatar-icon"
        fill="#ffffff"
        font-size="32"
        text-anchor="middle"
        x="32"
        y="45"></text>
    </svg>

And the result:
![SVG font icon avatar]({{ site.url }}/assets/media/2016-5-15-react-avatar-component/2.png)

Also pay attention to the avatar-icon css class, that class is used to enforce the font of the text tag other the icon can not be displayed correct. You can easily modify the css file of the html demo file (mentioned previously) to have this class. Like this:

    @font-face {
        font-family: 'avatar-icon';
        src:    url('fonts/avatar-icon.eot?nt4kp5');
        src:    url('fonts/avatar-icon.eot?nt4kp5#iefix') format('embedded-opentype'),
            url('fonts/avatar-icon.ttf?nt4kp5') format('truetype'),
            url('fonts/avatar-icon.woff?nt4kp5') format('woff'),
            url('fonts/avatar-icon.svg?nt4kp5#avatar-icon') format('svg');
        font-weight: normal;
        font-style: normal;
    }

    .avatar-icon {
      font-family: 'avatar-icon' !important;
      speak: none;
      font-style: normal;
      font-weight: normal;
      font-variant: normal;
      text-transform: none;
      line-height: 1;
      -webkit-font-smoothing: antialiased;
      -moz-osx-font-smoothing: grayscale;
    }

Now we know the magic of SVG in this use-case. Let build a React component around it.

# Build up a React component around SVG

I feel that this use of SVG is best match when integrate with React. It is truly a pure component (except the dependency on the font file but it is not the case), just render to html depend on its input. No performance issue, easy to reuse and modify.

And this is a basic component with support all image, initials, icon font avatar:

    export default class Avatar extends Component {
      render() {
        let inner = null;
        const {
          src,
          customClass,
          icon,
          color,
          size: height,
          size: width,
        } = this.props;

        if (src) {
          inner = <img src={src} className={customClass}/>
        } else {
          inner = (
            <svg height={height} width={width} className={customClass}>
              <rect fill={color} x="0" y="0" height={height} width={width}></rect>
              <text
                className="avatar-icon"
                fill="#ffffff"
                fontSize={width / 2}
                textAnchor="middle"
                x={width / 2}
                y={width / 2 + width / 4}>
                  {icon}
              </text>
            </svg>
          );
        }

        return <div className="avatar">{inner}<div>;
      }
    }

Just that simple and we have a work-well, useful avatar component.
You can even do more things with the SVG tag. In my examples, I just draw a square background for simplicity but you can draw other basic shapes like triangle, circle to be the background or whatever figures your imagination let you.

# Disadvantages

Despite of many advantages when using SVG, there are some disadvantages you will need to consider before using it for your project

- Have to specify the width and height for all avatars
- Because we need to specify the width and height to draw the SVG, the css width and height attribute will not affect tags inside the SVG tag.
- Using SVG this way put a limitation on responsive ability
