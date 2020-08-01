## granite-lit-css-modularizer


`granite-lit-css-modularizer` is a small [Node](https://nodejs.org) script to generate JavaScript Modules (ESM) from CSS libraries to integrate them in [LitElement](https://lit-element.polymer-project.org/) web components. 

The goal is to easily apply CSS libraries (like [Bootstrap](https://getbootstrap.com/), [Bulma](https://bulma.io/) or [Spectre](https://picturepan2.github.io/spectre/)) to LitElement web components.

This is done by generating ESM exporting [lit-element CSS Template Result](https://lit-element.polymer-project.org/api/classes/_lit_element_.cssresult.html) created from the CSS files. 



### Ready to use style modules

While we give here the instructions to create the style modules for any library, we already have some projects ready off-the-shelf, that you can simply add as dependencies in your projects and use them:

- Bootstrap: [`granite-lit-boostrap`](https://github.com/lostinbrittany/granite-lit-bootstrap)
- Bulma: [`granite-lit-bulma`](https://github.com/lostinbrittany/granite-lit-bulma)
- Spectre: [`granite-lit-spectre`](https://github.com/lostinbrittany/granite-lit-spectre)

### Generating the ESM exporting the CSS (a.k.a. style modules)

#### 1. Create a project for your CSS library

In my example, we want to generate style modules for [Bootstrap](https://getbootstrap.com/), so we are creating `granite-lit-bootstrap`. 

> We want to base my module on Bootstrap 4.5.2, so I name my version  `4.5.2-0`, as we want to follow Bootstrap versioning. If we need to update my version of the style modules based on this 4.5.2 version, we will increase the version (4.5.2-1, 4.5.2-2...).

Let's begin with this `package.json`:

```json
{
  "name": "@granite-elements/granite-lit-bootstrap",
  "description": "Wrapping of *Bootstrap CSS* as a LitElement CSS TemplateResult to be used in LitElement web components",
  "main": "granite-lit-bootstrap-min.js",
  "module": "granite-lit-bootstrap-min.js",
  "author": "Horacio Gonzalez <horacio.gonzalez@gmail.com>",
  "license": "Apache 2.0",
  "repository": {
    "type": "git",
    "url": "git://github.com/lostinbrittany/granite-lit-bootstrap.git"
  },
  "homepage": "https://github.com/lostinbrittany/granite-lit-bootstrap",
  "version": "4.5.2-0"
}
```

#### 2. Add `granite-lit-css-modularizer` as a devDependency

```bash
$ npm add --save-dev granite-lit-css-modularizer
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN @granite-elements/granite-lit-bootstrap@4.5.0-0 license should be a valid SPDX license expression

+ granite-lit-css-modularizer@1.0.1
added 7 packages from 3 contributors and audited 7 packages in 0.787s
found 0 vulnerabilities
```


#### 3. Add the CSS library you want to modularize  (e.g. Bootstrap) as a devDependency


```bash
$ npm add --save-dev bootstrap@4.5.2
npm WARN bootstrap@4.5.2 requires a peer of jquery@1.9.1 - 3 but none is installed. You must install peer dependencies yourself.
npm WARN bootstrap@4.5.2 requires a peer of popper.js@^1.16.1 but none is installed. You must install peer dependencies yourself.
npm WARN @granite-elements/granite-lit-bootstrap@4.5.0-0 license should be a valid SPDX license expression

+ bootstrap@4.5.2
added 1 package from 2 contributors and audited 8 packages in 2.046s

1 package is looking for funding
  run `npm fund` for details

found 0 vulnerabilities
```

#### 4. Generate the style module

Using NodeJS and the `granite-lit-css-modularizer.js` to transform the library's CSS files into polymer elements.

In our Bootstrap example, CSS are in `dist/css` folder in the Bootstrap module, so we can do:

```bash
$ node ./node_modules/granite-lit-css-modularizer/granite-lit-css-modularizer.js ./node_modules/bootstrap/dist/css ./css_modules/granite-bootstrap bootsrapStyles
```

> The last parameter is the export name, i.e. the name under which the CSS Template Result will the exported in the style module. If no name is given, it will use `styles`.

After executing it, a series of HTML files is generated in the `./css_modules/granite-bootstrap` folder, each one corresponding to a Bootstrap CSS file.

```bash
$ ls css_modules/granite-bootstrap/
granite-lit-bootstrap-grid-min.js  
granite-lit-bootstrap-min.js         
granite-lit-bootstrap-reboot.js
granite-lit-bootstrap-grid.js      
granite-lit-bootstrap-reboot-min.js  
granite-lit-bootstrap.js
```


### Using the style modules 

Using the style modules in any LitElement component is an two step process:

#### 1. Import one of the style module in the element where you want to use it

In our Bootstrap case, we will simply want to import `granite-lit-bootstrap.js` (wrap around `bootstrap.css`) or `granite-lit-bootstrap-min.js` (wrap around `bootstrap.min.css`).

Supposing you want to import `granite-lit-bootstrap.js`:
 
```
import {bootstrapStyles} from './css_modules/granite-bootstrap/granite-lit-bootstrap.js';
``` 

#### Inside your component, use the exported style in the static `styles` property


```js

class GraniteBootstrapExample extends LitElement {
  static get styles() {
    return [bootstrapeStyles];
  }
  render() {
    return html`
      <div class="label label-rounded label-primary">Styled text</div>
    `;
  }
```

#### A complete example

```js
import { html, LitElement } from 'lit-element';
import {bootstrapStyles} from './css_modules/granite-bootstrap/granite-lit-bootstrap.js';

class GraniteBootstrapExample extends LitElement {
  static get styles() {
    return [bootstrapStyles];
  }
  render() {
    return html`
      <table class="table  table-hover">
          <tr><th>Surname</th><th>Name</th></tr>
          ${this.people.map( (person) => {
            return html`
            <tr>
              <td>${person.lastname}</td>
              <td>${person.firstname}</td>
            </tr>
            `;
          })}
      </table>
    `;
  }

  static get properties() {
    return {
      people: { type: Array },
    };
  }

  constructor() {
    super();
    this.people = [
      { firstname: 'Jack', lastname: 'Aubrey' },
      { firstname: 'Anne', lastname: 'Elliot' },
      { firstname: 'Stephen', lastname: 'Maturin' },
      { firstname: 'Emma', lastname: 'Woodhouse' },
    ];
  }
}
customElements.define('granite-bootstrap-example', GraniteBootstrapExample);

```

### Contributing

1. Fork it!
2. Create your feature branch: `git checkout -b my-new-feature`
3. Commit your changes: `git commit -m 'Add some feature'`
4. Push to the branch: `git push origin my-new-feature`
5. Submit a pull request :D

### Note on semver versioning

I'm aligning the versions of this element with Bootstrap version, in order to make easier to choose the right version
 
### License

[Apache 2.0](http://www.apache.org/licenses/LICENSE-2.0)
