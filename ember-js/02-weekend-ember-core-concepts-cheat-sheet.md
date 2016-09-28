## Terminology
<hr>

* **Route Handler**:  Renders a template and loads a model that is then available to that template. 

* **Model**: Represent persistent state, and typically persist information to a web server. Models outline the attributes of objects. 

* **Components**: Consists of **two** files: A template written in Handlebars, and a source file written in JavaScript. The files have the same name, but the template ends in the Handlebars `.hbs` extension, and the source file ends in the `.js` extension. The template defines what the component should _look_ like and the source file controls what _functionality_ that component has. Components are called from within templates, or within the `.hbs` portion of another component.

* **Template**:  Define how information is displayed, and organize the layout of HTML in an application. Ember templates use the syntax of [Handlebars](http://handlebarsjs.com) templates. Anything that is valid Handlebars syntax is valid Ember syntax. Templates can also display properties provided to them from their context, which is either a component or a route.

