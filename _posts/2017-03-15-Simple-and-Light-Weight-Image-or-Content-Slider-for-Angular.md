---
layout: post
title: Simple and Lightweight Image Gallery and Content Slider for Angular by Ravinder Payal
author: ravinder_payal
---
<p>
Hi friends, today, we are going to develop a simple to use image gallery and content slider component using (and for) Angular with transitions and styling done using CSS. Some of us might be thinking that why re-invent the wheel when libraries like JSSOR, and plugins like JQUERY carousel are already available. So, we are re-creating it because:-
</p>
1.  There are no good native Angular 2( or 4 ) sliders available. Those available are too heavy, and have additional dependencies like JQUERY and Bootstrap, which makes our project pointlessly more heavy.
2.	No worries about licensing.
3.	To get a clear understanding of how slide shows and transitions work on web pages.
4.	Light weight solution, because the ready to use libraries have a lot of boiler plate code for making them work at all.
5.	To have complete control over code.
6.	Tell me if, I missed any other important point.

<!-- Place this tag where you want the button to render. -->
<a class="github-button" href="https://github.com/ravinderpayal/Angular-Image-And-Content-Slider/archive/master.zip" data-icon="octicon-cloud-download" data-style="mega" aria-label="Download ravinderpayal/Angular-Image-And-Content-Slider on GitHub">Download</a> <!-- Place this tag where you want the button to render. -->
<a class="github-button" href="https://github.com/ravinderpayal/Angular-Image-And-Content-Slider" data-icon="octicon-star" data-style="mega" data-count-href="/ravinderpayal/Angular-Image-And-Content-Slider/stargazers" data-count-api="/repos/ravinderpayal/Angular-Image-And-Content-Slider#stargazers_count" data-count-aria-label="# stargazers on GitHub" aria-label="Star ravinderpayal/Angular-Image-And-Content-Slider on GitHub">Star</a>


<script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<ins class="adsbygoogle"
     style="display:block; text-align:center;"
     data-ad-format="fluid"
     data-ad-layout="in-article"
     data-ad-client="ca-pub-3201220427379470"
     data-ad-slot="2921542940"></ins>
<script>
     (adsbygoogle = window.adsbygoogle || []).push({});
</script>

If you agree, let's go ahead.

First of all we will prepare our HTML structure.

### Template

```html
<div class="slider">
  <div class="sliderArrows">
    <a (click)="backWard()">&lt;<!--Use icons of your choice, although these simple Lower than and greater signs also looks well--></a>
    <a (click)="forWard()">&gt;</a>
  </div>
  <ul class="slideShow">
    <li *ngFor="let li of slides" [ngClass]="li?.classes">
        <printSlide [meta]="li"></printSlide>
    </li>
  </ul>
</div>
```
In our template we have a root div element, with class "slider", for holding our slider and controlling height and width of our slider. Next is `div.sliderArrows` which contains arrows for sliding our images righ or left, and consumes flex layout for correctly spacing arrows on x-axis.
Last is `ul.slideShow` which contains our slides for sliding left or right.We uses `*ngFor` directive for adding our slides in dom. This also gives the dynamic behaviour to slider, as we can change our array, containing images, dynamically. Removing a slide from slideshow is as simple as removing the index/element containing detail of slide from array. We are using a custom element/component named printSlide for adding aditional features and keeping our template code yet simple.

### Let's have a look at printSlide component.

>*safeHtml pipe is added for preventing the style attributes( applied by you) from getting removed by Angular2 XSS security libraries*
>[Read more here https://angular.io/guide/security](https://angular.io/guide/security)


```typescript
import { DomSanitizer } from '@angular/platform-browser'

@Pipe({ name: 'safeHtml'})
export class SafeHtmlPipe implements PipeTransform  {
  constructor(private sanitized: DomSanitizer) {}
  transform(value) {
    return this.sanitized.bypassSecurityTrustHtml(value);
  }
}
@Component({
    selector:"printSlide",
    template:`
        <div *ngIf="meta.sType=='div'" [innerHtml]="meta.content | safeHtml">

        </div>
        <img [src]="meta.imgSrc" *ngIf="meta.sType=='img'" />
    `
})
export class printSlide{
    @Input("meta") meta:any;
    constructor(){

    }
}
```
Here, it's clearly visible the use of printSlide component. It allows us to show a wide range of content for sliding, without making the main componet complex. We can add more features in printSlide component like Ajax DIV or LazyLoading of Images or Youtube Videos.
<script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<ins class="adsbygoogle"
     style="display:block; text-align:center;"
     data-ad-format="fluid"
     data-ad-layout="in-article"
     data-ad-client="ca-pub-3201220427379470"
     data-ad-slot="2921542940"></ins>
<script>
     (adsbygoogle = window.adsbygoogle || []).push({});
</script>
### Now come to the actual component we have made.
```typescript
import { Component, ElementRef, Renderer, Input, Output, Optional, EventEmitter, ViewEncapsulation } from '@angular/core';

@Component({
    selector: 'contentSlider',
    template: `
<div class="slider">
<div class="sliderArrows">
                <a (click)="backWard()"><</a>
                <a (click)="forWard()">></a>
</div>
<ul class="slideShow">
        <li *ngFor="let li of slides" [ngStyle]="{'display':li?.hidden?'none':'unset'}" [ngClass]="li?.classes">
            <printSlide [meta]="li"></printSlide>
        </li>
</ul>
</div>
    `,
    styleUrls:["style.css"],
    encapsulation:ViewEncapsulation.None
})
export class contentSlider {
    /**
     * Play Interval
     */
    @Input('playInterval') interval:any = 2000;
    slides:any;
    
    @Input("slides") set _slides(s){
        this.slides = s;
        console.log(this.slides);
        this.number = this.slides.length;
        if(this.slides.length)
            this.slides[0]["classes"] = ["active"];
    }

    @Input('autoPlay') set _autoPlay(b:boolean){
        this.autoPlay = b
        if(b){
            this.auto(this.interval);
        }
    }
    currentElement:number = 0;
    autoPlay = false;
    number:number=0;
    //lis:number = 0;
    intervalTime:number = 1000;//in ms(mili seconds)
    private delayHideSetTimeOutControl:any;
    constructor(){
        //this.slideShow=document.getElementById("slideShow");

        //this.lis = this.slides.length;

        //this.number=this.lis.length;
    }
    backWard(){
        if(this.autoPlay)
            clearInterval(this.interval);
        this.currentElement=this.currentElement-1;
        if(this.currentElement<0){
            this.currentElement=this.number-1;
        }
        this.removeClasses();
        var prev=this.currentElement==this.number-1?0:this.currentElement+1;
        //this.lis[prev].classList.add("animateForward");
        this.slides[prev].classes = ["animateForward"];
        this.show(this.slides[prev]);
        this.show(this.slides[this.currentElement]);

        clearTimeout(this.delayHideSetTimeOutControl);

        this.delayHideSetTimeOutControl=this.delayHide(this.slides[prev],1100);
        //this.lis[this.currentElement].classList.add("active");
        this.slides[this.currentElement].classes = ["active","backward"];
        //this.lis[this.currentElement].classList.add("backward");
        if(this.autoPlay)this.auto(this.intervalTime);
    }

    removeClasses(){
        for(var i=0;i<this.number;i++){
            this.slides[i].classes = {}
        }
    }
    forWard(){
        console.log("forward called")
        if(this.autoPlay)clearInterval(this.interval);
        this._forWard();
        if(this.autoPlay)this.auto(this.intervalTime);        
    }
    private _forWard(){
        this.currentElement=1+this.currentElement;
        //this.lis=this.slideShow.childNodes;
        if(this.currentElement>=this.number){
            this.currentElement=0;
        }
        this.removeClasses();
        var prev=this.currentElement==0?this.number-1:this.currentElement-1;
        //this.lis[prev].classList.add("animateBack");
        console.log(this.slides[prev]);
        this.slides[prev]["classes"] = ["animateBack"];

        this.show(this.slides[prev]);
        this.show(this.slides[this.currentElement]);

        clearTimeout(this.delayHideSetTimeOutControl);
        this.delayHideSetTimeOutControl=this.delayHide(this.slides[prev],1100);
        //this.lis[this.currentElement].classList.add("active");
        //this.lis[this.currentElement].classList.add("forward");
        this.slides[this.currentElement].classes = ["active","forward"];
    }
    auto(ms){
        this.autoPlay=true;
        this.intervalTime=ms;
        this.interval=setInterval(this._forWard.bind(this),ms);
    }
    delayHide(el,ms){
        return setTimeout(()=> el.hidden = true,ms);
    }
    show(el){
        el.hidden = false;
    }
}
```

The component is really very simple. It has three main methods named as auto, _forWard and backWard. With one more method (forWard ) as a wrapper of forWard function for exposing it to arrows in tempelate. We need not a _backWard and it's wrapper because, we are not going to call backWard function from `setInterval` defined in `auto` method of component. When moving slides with arrows we need to reset the interval so that it doesn't mess up with manual event. The method auto is for setting ‘keep playing’ mode slider ON.
We are defining classes for our slides in their array itself with property named classes like this `this.slides[this.currentElement].classes = {"active":true,"forward":true}`. On slides/li elements, we are using [`*ngClasses`](https://angular.io/docs/ts/latest/api/common/index/NgClass-directive.html) directive. Let's assume an interval started at 12:00:00 AM, and user clicked for next slide at 12:00:01. In this case the next slide will be only visible for 1 sec instead of 2 sec(default setting), if the interval is not cleared just after the click event. I hope it's clear, why we used _forWard and forWard.
<script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<ins class="adsbygoogle"
     style="display:block; text-align:center;"
     data-ad-format="fluid"
     data-ad-layout="in-article"
     data-ad-client="ca-pub-3201220427379470"
     data-ad-slot="2921542940"></ins>
<script>
     (adsbygoogle = window.adsbygoogle || []).push({});
</script>
Now, we will move to designing and correctly structuring of our slider.

### Required CSS code for our Content
```css
/*---------------------------Image Slider--------------------------*/
.slider{
    height:400px;
    width:100%;
    font-size: 500%;
    color:#000;
    z-index: 0;
    background: #fff;
    position: relative;
}

.slider ul{
    display: flex;
    padding: 0;
    justify-content: center;
    margin: 0;
}
.slider ul li {
    list-style: none;
    width: 100%;
    display: none;
    /*transition: cubic-bezier(0.6, -0.61, 0, 1.34) all 0.7s;*/
}
.slider ul li img{
    max-width:100%;
    min-width:100%;
    height: 100%;
    display: block;
}

.slider ul li.active{
    display: inline-block;
}
.slider ul li.backward{
    animation: slideShow0 1.2s cubic-bezier(0.6, -0.61, 0, 1.34);
}
.slider ul li.forward{
    animation: slideShow1 1.2s cubic-bezier(0.6, -0.61, 0, 1.34);
}
.slider ul li.animateBack{
    display: inline-block;
    position: absolute;
    animation: slideShow2 1s cubic-bezier(0.38,-0.74, 0, 1.29) forwards;
    animation-delay: 0.1s;
}
.slider ul li.animateForward{
    display: inline-block;
    position: absolute;
    animation: slideShow3 1s cubic-bezier(0.38,-0.74, 0, 1.29)  forwards;
    animation-delay: 0.1s;
}
.slider .sliderArrows{
    display:flex;
    position: absolute;
    flex-flow:row wrap;
    justify-content:space-between;
    width: 100%;
    align-items:center;
    height: 100%;
    z-index: 101;
}
.slider .sliderArrows a{
    cursor:pointer;
    font-weight: 900;
}


@keyframes slideShow0{
    0%{
         translate:-1000px;
         visibility: hidden;
         opacity: 0;
    }
    100%{
        translate:0px;
        visibility: visible;
        opacity:1;
    }

}


@keyframes slideShow1{
    0%{
         translate:1000px;
         visibility: hidden;
         opacity: 0;
    }
    100%{
        translate:0px;
        visibility: visible;
        opacity:1;
    }

}

@keyframes slideShow2{
     0%{
        display: inline-block;        
        translate:0px;
        visibility: visible;
        opacity: 1;
     }
    100%{
        translate:-1000px;
        visibility: hidden;
        opacity: 0;
    display: none;
        /*display: none;*/
     }

}

@keyframes slideShow3{
     0%{
        display: inline-block;        
        translate:0px;
        visibility: visible;
        opacity: 1;
     }
    100%{
        translate:1000px;
        visibility: hidden;
        opacity: 0;
    display: none;
        /*display: none;*/
     }
}
/*---------------------------Image Slider Ends----------------------------*/
```
One thing important in this code is that we have used flex-box property for arrow container, which makes our arrows responsive and align vertically and horizontally at exactly right places. Second thing is that we have defined animation key frames for 4 types of transitions we will be adding to our Slider. 

>We can add any number of different transitions, but showing different types of transition effects is not the objective of this tutorial, and we will be doing to a separate tutorial for that. But, you can suggest some good slide show transistions, which we may try together to create.

<script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<ins class="adsbygoogle"
     style="display:block; text-align:center;"
     data-ad-format="fluid"
     data-ad-layout="in-article"
     data-ad-client="ca-pub-3201220427379470"
     data-ad-slot="2921542940"></ins>
<script>
     (adsbygoogle = window.adsbygoogle || []).push({});
</script>
### Let’s know about the key frames we are using here.

First key frame is `SlideShow0`:-
```css
@keyframes slideShow1{
    0%{
         translate:1000px;
         visibility: hidden;
         opacity: 0;
    }
    100%{
        translate:0px;
        visibility: visible;
        opacity:1;
    }
}
```
This frame is used for animating the appearing image/element (In case of non image slide-shows). It first translates/moves the image to 1000px right side from its current position, and then it brings back to its current position which gives image sliding effect (you name it better). Along with position transition, it also gives an opacity transition. Opacity and Visibility can be used interchangeably.
```css
@keyframes slideShow1{
    0%{
         translate:1000px;
         visibility: hidden;
         opacity: 0;
    }
    100%{
        translate:0px;
        visibility: visible;
        opacity:1;
    }

}
```
SlideShow1 is for backward animation of appearing image. Similarly, SlideShow2 and 3 are for backward and forward animation of disappearing images.
```css
.slider ul li.active{
    display: inline-block;
}
.slider ul li.backward{
    animation: slideShow0 1.2s cubic-bezier(0.6, -0.61, 0, 1.34);
}
.slider ul li.forward{
    animation: slideShow1 1.2s cubic-bezier(0.6, -0.61, 0, 1.34);
}
.slider ul li.animateBack{
    display: inline-block;
    position: absolute;
    animation: slideShow2 1s cubic-bezier(0.38,-0.74, 0, 1.29) forwards;
    animation-delay: 0.1s;
}
.slider ul li.animateForward{
    display: inline-block;
    position: absolute;
    animation: slideShow3 1s cubic-bezier(0.38,-0.74, 0, 1.29)  forwards;
    animation-delay: 0.1s;
}
```
It’s the use of key frames. One thing worth noting is that we are using forwards property of animation-fill-mode ( animation-fill-mode: forwards;) and cubic-bezier is used for defining the curve which the timing function of animation will follow.

>Note: - Chrome has a good graphical tool for defining curve, and shows a live demo of animation with defined curve. Just click on the curve sign appearing before curve function in CSS tab in developer tools.

<script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<ins class="adsbygoogle"
     style="display:block; text-align:center;"
     data-ad-format="fluid"
     data-ad-layout="in-article"
     data-ad-client="ca-pub-3201220427379470"
     data-ad-slot="2921542940"></ins>
<script>
     (adsbygoogle = window.adsbygoogle || []).push({});
</script>
### Basic Usage

```typescript
    @Component({
        selector: 'ImageShow',
        template: `
        <contentSlider [slides]="images"></contentSlider>
        `
    })
    export class ImageShowComponent implements  AfterViewInit{
       images:Array<any> = [{"sType":"img","imgSrc":"..."},{"sType":"div","content":"...Hello It's slidable content"}];
      constructor(){
      }
    }
```
Download the complete source code from [github repository](https://github.com/ravinderpayal/Angular2-Image-OR-Content-Slider).
<!-- Place this tag in your head or just before your close body tag. -->
<script async defer src="https://buttons.github.io/buttons.js"></script>
<!-- Place this tag where you want the button to render. -->
<a class="github-button" href="https://github.com/ravinderpayal/Angular2-Image-OR-Content-Slider/archive/master.zip" data-icon="octicon-cloud-download" data-style="mega" aria-label="Download ravinderpayal/Angular2-Image-OR-Content-Slider on GitHub">Download</a>&nbsp;<!-- Place this tag where you want the button to render. -->
<a class="github-button" href="https://github.com/ravinderpayal/Angular2-Image-OR-Content-Slider" data-icon="octicon-star" data-style="mega" data-count-href="/ravinderpayal/Angular2-Image-OR-Content-Slider/stargazers" data-count-api="/repos/ravinderpayal/Angular2-Image-OR-Content-Slider#stargazers_count" data-count-aria-label="# stargazers on GitHub" aria-label="Star ravinderpayal/Angular2-Image-OR-Content-Slider on GitHub">Star</a>
Thanks Friends for reading to end. If you liked the post please share it, and star our project's [github repository](https://github.com/ravinderpayal/Angular2-Image-OR-Content-Slider)
