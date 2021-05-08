  

# Let's make the web fast and native

  

  

I've talked about Web Components a lot now, but what should they mean to you. A Web Component could be any modular, encapsulated and reusable HTML element. Containing as little or as much of functionality as you'd like. So let's try and create some.

  

  

## HTMLElement

  

  

Web Components are completely native to html and JS, therefore you do not need any special setup or imports.
```javascript
class MyAwesomeComponent extends HTMLElement {
  connectedCallback() { console.log('I'm an awesome component') }
}
customeElements.define('my-awesome-component', MyAwesomeComponent);
```
```html
<my-awesome-component></my-awesome-component>
```
   

This is the bare basics of custom Web Components, which has enough lifeCycle methods to make things interesting, but it's not enough to board the ship. Which is why we'll use litElement and litHTML. Two libraries making their way down the path of becoming web standards.
 

## lit

> [lit-html](https://github.com/Polymer/lit-html) is an efficient,
> expressive and extensible HTML templating library for JavaScript. It
> lets you write HTML templates in JavaScript, then efficiently render
> and re-render those templates together with data to create and update DOM:  
 


> [lit-element](https://github.com/Polymer/lit-element) is a simple
> base class for creating fast and lightweight web components with
> lit-html.

  

  

Settings things up is always bad, so we'll be using [webcomponents.dev](https://webcomponents.dev/), as it takes away the pain of trying to install packages during a workshop.

  

  

As shown in the presentation, [lit-html](https://github.com/Polymer/lit-html) is nothing more than a templating library.

  

  
```javascript
import { html } from 'lit-html';
const myAwesomeTemplate = html`<h1>hello awesome world!</h1>`
```

  

  

In itself this is very fast and powerful, but combined with [lit-element](https://github.com/Polymer/lit-element) it is meant to rule the web. litElement is a class extending HTMLElement, used to create WebComponents, adding a bunch of lifeCycle methods, data-binding and render.

  

  
```javascript
render() {
  return html`<h1>hello awesome world!</h1>`
}
```

  

  

## The idea of the workshop

  

  

The strongsuit of Web Components is to have a library of components customizable, ready to go and encapsulated. So let's create our own library of components we can use for any project we want.

  

  

Starting off with the basics. Web Components can be anything: a link, a button, a navigation item, the entire header, the encapsulation of the paragraph you're reading at the moment. Anything.

  

It is up to you to determine them and structure your components in a way that is efficient and sensible. Every specific set of functionality could be a component, but there's no need in reinventing the wheel, a basic paragraph with some styling could technically be one. But if there's no added value, why even do it?

  

  
```css
p{ color: red }
```
```html
<p>Hey</p>
```

should not become

```html
<my-paragraph>Hey</my-paragraph>
```
  

  

So there's a fine line of what is handy, and what is ridiculous.

  

  

## components

  

Let us start with the basics.

  

Go to the [repo](https://webcomponents.dev/edit/GRWbe4XeJVxGrG6mQsZP/src/index.js) and fork it so you have your own space to work in.

  

  

### button

  

Everything starts with a good button.

  

`/components/button/ia-button.js` contains the file to start with.

  

We want to create a button that has all functionalities we could wish for.

  

  

#### basic button

  

Add the underneath snippet to the html tag in the render function, save and it should appear in the preview window.

  

*I took the liberty of already adding some styling, because the standard button is ugly. I encourage you to play with the styling as well as it is a big part of creating custom components.*

  

  
```html
<button value="Submit">Submit</button>
```
  

  

This is the most basic we can get, a static button. If you go to `/index.js` you can duplicate the button and see them appear as fully standalone instances of the same item.

  

  

Of course a static custom webcomponent is ridiculous. So let's upgrade the render function.

```javascript
static get properties() {
  return {
    value: { type: String},
  }
}
render() {
  return html`
    <button  value="${this.value}">${this.value}</button>
  `;
}
```

Now we can pass a variable to each instance of the component.

  

  
```html
<ia-button value="Lorem"></ia-button>
<ia-button value="Ipsum"></ia-button>
```
  

  

Buttons are clickable, but at the moment we have nothing to catch a click. So let's add an event listener.

  

Standard button clicks are bubbled events, meaning they pass up from child to parent. Therefore we can catch it immediately on our `<ia-button>` as such

  

  
```html
<ia-button value="bar" @click=${e => console.log('foo', e)}></ia-button>
```
  

  

So now we have 2 out of 3 characteristics of a button set for us.

  

  

- event handling

  

- text customizability

  

but not styling.

  

This is a fun part. As shown in the presentation, a button isn't just a button. Every button has its own functionality. Icon buttons, inline buttons, primary, secondary, etc.

  

  

Take a look at webcomponents.dev for instance, the top right has three buttons. Fork, share and new. Which could all be derived from the same component even though they don't look similar. Let's do that.

  

  

Secondary buttons are less eye catchy than primary buttons. Less catchy styling, outlined, etc.

  

  

Let's first clean up the styling I put in place. The big difference we want for a primary and secondary button is the color and border. So let's remove those characteristics from the button selector.

  

CSS has a special html tag selector we can use.

  

  

Move all styling you want specific to the primary and secondary button in these selectors:

  

  
```css
:host([primary]) button {}
:host([secondary]) button {}
```
When you save this, the styling looks weird, because we haven't added the primary or secondary tag yet.

Go to `index.js` and add primary or secondary to the button like so:

  

`<ia-button primary value="foo"></ia-button>`

  

  

The styling I used:
```css
:host([primary]) button {
	background-color: #4CAF50;
	border: 2px solid #4CAF50;
}

:host([primary]) button:hover {
	background-color: #37903b;
	border: 2px solid #37903b;
}

:host([secondary]) button {
	background-color: #bfbfbf;
	border: 2px solid white;
}

:host([secondary]) button:hover {
	background-color: #cfcfcf;
}
```

  

  

Cool! We now can make buttons for each special functionality, and still have only one component.

  

  

### Creating a global styling

  

One of the nicest things to have with Web Components is having an encapsulated something. But when we're creating a library of components some styling parameters, like colours or margin, etc. need to be reused.

  

  

What I did above is a bad practice in that situation, imagine copy pasting one colour in 20 different components. We can fix this.

  

  

Given that we're using core javascript and templating, a colour or margin is nothing more than a variable, which we already know how to implement. Now let's do it on a global scale.

  

  

In `style/style.js` we can define our globally used styling.

  

  
```javascript
export const primaryGreen = css`#4CAF50`;
```

  

  

Colours are not the only thing you can define in a global styling sheet. Margins, paddings, font-sizes, etc. are often predefined to give a consistent look and feel of a website.

  

*a big extra plus is that you only have to change a single parameter to change it across every component*

  
Our button is now finished
  

### card

  

  

A card is a widely used template or item on different kinds of sites. A news article snippet, blogpost or tweet could all be contained in a small content card.

  

It's nothing more than a predefined container for content. Let's make it.

  

  

`card/cards.js` is not yet imported in `index.js`, so make sure to do so, just like the `ia-button`.

  

  

Cards are just a div that contain something. So the usage would look like this:

  
```javascript
<ia-card>
  <p>This is the content of my card</p>
</ia-card>
```
  
But how do we get the content "This is the content of my card" to display inside the ia-card. If you add the above, it will not display.

That's where [slot](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/slot) comes into play.

```javascript
return html`
  <div>
    <div>
      <slot name="content">
    </div>
  </div>
`;

<ia-card>
  <p slot="content">title</p>
</ia-card>
```

Add the following styling

```css
.container {
  min-width: 120px;
  margin: 20px;
  padding: 10px;
  background-color: #fafaff;
  border-radius: 10px;
  box-shadow: 9px  8px  5px  0px  rgba(0,0,0,0.08);
  -webkit-box-shadow: 9px  8px  5px  0px  rgba(0,0,0,0.08);
  -moz-box-shadow: 9px  8px  5px  0px  rgba(0,0,0,0.08);
}
```

This card could now be the foundation to create loading icon cards, posts, containers, etc.

## interval
The true power in Web Components is not to only make a library of usefull stuff, but to combine all components we have into something bigger. 
We now have a button and a card, let's make a third component where we combine them for something useful. 

import both components into `interval/interval.js`

```javascript
import  '../button/ia-button.js';
import  '../card/ia-card.js';
```

and import the interval component into `index.js`
We want to use both the card and the button.

```javascript
render() {
  return  html`
    <ia-card>
      <div  slot="content">
        <ia-button  value="add time"  primary></ia-button>
      </div>
    </ia-card>
  `;
}
```
we want to display every current time when we press the button.
So let's add an event listener to the click and push the current time to a property called `times`. And let's instantiate the property "times" to an empty array.

```javascript
class InterActiefIntervalClock extends LitElement {
  static get properties() {
    return {
      times: { type: Array },
    }
  }

  constructor() {
    super();
    this.times = [];
  }

  render() {
    return html`
          <ia-card>
            <div slot="content">
              <ia-button value="add time" primary @click="${() => this.addNewtime()}"></ia-button>
            </div>
          </ia-card>
	    	`;
  }

  addNewtime() {
    this.times = [...this.times, new Date()];
  }
}
```

This keeps track of our times, but we would of course like to display them as well. Changing a property defined in the `properties` getter automatically triggers a rerender of the changed values, unchanged items will not render again even if they're a part of the same render function. 

Arrays can be mapped via a template:

```javascript
timeTemplate(time) {
  return html`<p>${time.getHours()}:${time.getMinutes()}:${time.getSeconds()}</p>`;
}
```

Which we can trigger inside our content slot, under the button

```javascript
${this.times.map(this.timeTemplate)}
```
    
Each click will now save and display that time. 
If you go back to `index.js` you can duplicate the interval clock multiple times and each one will work indiviually. 
