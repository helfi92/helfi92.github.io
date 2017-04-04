---
layout: post
title: Code Splitting for React Router with Webpack and HMR
description: "Code Splitting for React Router with Webpack and HMR"
modified: 2017-04-01T15:27:45-04:00
tags: [taskcluster, neutrino, react, webpack, router]
---
In this guide we’ll create a [starter application](https://github.com/mozilla-neutrino/react-router-starter) that uses routes with code splitting.

![Cover](/assets/images/code-splitting/cover-picture.jpeg)

For big web apps, it’s not efficient to put all of your code into a single file. Large apps should only download the JavaScript required to boot the app. We want to be able to break a single file into many fragments. Splitting your code into multiple chunks is what we refer to as “code splitting”. Chunks will be loaded as the user navigates around.
[Neutrino](https://neutrino.js.org/), will be used to see how we can achieve code splitting with React, Webpack, and React Router. Neutrino is an awesome tool that harnesses the power of Webpack to let you create and build modern JavaScript applications with zero initial configuration. It works by allowing us to inject shared presets or configurations. We will be using the React preset. The latter requires you to be on Node.js v6.9+. According to the [Neutrino documentation](https://neutrino.js.org/presets/neutrino-preset-react/), the React preset features:

- Modern Babel compilation supporting ES modules, last 2 major browser versions, async functions, and dynamic imports
- Support for import CSS, HTML, images, fonts, and icons
- Tree-shaking to create smaller bundles
- Hot Module Replacement, no HTML templating, and much more

The entire source code of this post is [available on GitHub](https://github.com/mozilla-neutrino/react-router-starter). Without further ado, let’s get started.
First, we need a directory to start working from. From your terminal, create a new directory and change into it. Then we are going to install `neutrino`, `neutrino-preset-react` and `react-hot-loader` as development dependencies. We will also need `react`, `react-dom` and `react-router-dom` for actual React development. `react-hot-loader@3.0.0-beta-6` is the latest version at the time of this write-up.

```bash
❯ mkdir react-code-splitting
❯ cd react-code-splitting/
❯ yarn add --dev neutrino neutrino-preset-react
❯ yarn add --dev react-hot-loader@3.0.0-beta.6
❯ yarn add react react-dom react-router-dom
```

By default, the React preset looks for source code in a `src` directory with the main entry point being `index.js`.

```bash
❯ mkdir src && touch src/index.js
```

Let’s edit `index.js` with a simple hello world example. We’ll be changing that file later but for now, we will keep it simple for the sake of testing the bare minimum.

```javascript
import React from 'react';
import { render } from 'react-dom';

render(<h1>Hello world!</h1>, document.getElementById('root'));
```

Now edit your project’s `package.json` to add commands for starting and building the application:

```
{
  "scripts": {
    "start": "neutrino start --use neutrino-preset-react",
    "build": "neutrino build --use neutrino-preset-react"
  },
  "devDependencies": {
    "neutrino": "^5.2.0",
    "neutrino-preset-react": "^5.2.0"
  },
  "dependencies": {
    "react": "^15.4.2",
    "react-dom": "^15.4.2",
    "react-hot-loader": "3.0.0-beta.6",
    "react-router-dom": "^4.0.0"
  }
}
```

Start the app then open a browser to the address in the console.

```bash
❯ yarn start
✔ Development server running on: http://localhost:5000
✔ Build completed
```

![Hello World](/assets/images/code-splitting/hello-world.png)

Great, we’re now ready to split code like real ninjas :-)

To support Hot Module Replacement (HMR), change index.js, the previously created file to the following:

```javascript
// index.js
import React from 'react';
import { render } from 'react-dom';
import { AppContainer } from 'react-hot-loader';

import App from './App';

const renderApp = () => {
  render(
    <AppContainer>
      <App />
    </AppContainer>,
    document.getElementById('root'),
  );
};

// This is needed for Hot Module Replacement
if (module.hot) {
  module.hot.accept('./App', () => renderApp());
}

renderApp();
```

We added `AppContainer` to specify which part of the app we want HMR, and an if statement to activate it in development mode. We also imported `App.js` which we will create next.

```javascript
// App.js
import React from 'react';
import { BrowserRouter as Router, Route, Link } from 'react-router-dom';
import asyncComponent from './components/asyncComponent';

const Page1 = asyncComponent(() => import('./components/Page1')
  .then(module => module.default), { name: 'Page 1' });
const Page2 = asyncComponent(() => import('./components/Page2')
  .then(module => module.default), { name: 'Page 2' });
const Home = asyncComponent(() => import('./components/Home')
  .then(module => module.default), { name: 'Home' });

const App = () => (
  <Router>
    <div className="App">
      <ul>
        <li><Link to="/">Home</Link></li>
        <li><Link to="/page1">Page 1</Link></li>
        <li><Link to="/page2">Page 2</Link></li>
      </ul>

      <Route exact path="/" component={Home} />
      <Route path="/page1" component={Page1} />
      <Route path="/page2" component={Page2} />
    </div>
  </Router>
);

export default App;
```

`App.js` imports `react-router-dom` and returns the Router component with the routes injected.

## Dynamic Routes

From your terminal, create routes to use. We will also need `asyncComponent.js` to lazily load components.

```bash
❯ mkdir src/components
❯ touch src/components/Page1.js
❯ touch src/components/Page2.js
❯ touch src/components/Home.js
❯ touch src/components/asyncComponent.js
```

We’ll fill in the bare minimum for the routes.

```javascript
// Page1.js
import React from 'react';

const Page1 = (props) => <h1>{props.name}</h1>;

export default Page1;
// Page2.js
import React from 'react';

const Page2 = (props) => <h1>{props.name}</h1>;

export default Page2;
// Home.js
import React from 'react';

const Home = (props) => <h1>{props.name}</h1>;

export default Home;
```

We’re almost done. Our last task is to write logic to lazily load components. We will do that through `asyncComponent.js`.

```javascript
// asyncComponent.js
import React from 'react';

export default (loader, collection) => (
  class AsyncComponent extends React.Component {
    constructor(props) {
      super(props);

      this.Component = null;
      this.state = { Component: AsyncComponent.Component };
    }

    componentWillMount() {
      if (!this.state.Component) {
        loader().then((Component) => {
          AsyncComponent.Component = Component;

          this.setState({ Component });
        });
      }
    }

    render() {
      if (this.state.Component) {
        return (
          <this.state.Component { ...this.props } { ...collection } />
        )
      }

      return null;
    }
  }
);
```

And we’re done!

## Quick Start

Start the app with yarn start, then open a browser to `http://localhost:5000`.

```bash
❯ yarn start
✔ Development server running on: http://localhost:5000
✔ Build completed
```

![Quick Start](/assets/images/code-splitting/quick-start.png)

Open the network tab to see the individual chunks being loaded lazily.

![Network](/assets/images/code-splitting/network.png)

## Building

The React preset builds static assets to the build directory by default.

![Building](/assets/images/code-splitting/building.png)

To conclude, we used [Neutrino](https://github.com/mozilla-neutrino/neutrino-dev) to roll up a React application and split our code into multiple chunks. We also incorporated HMR to inject updated modules without reloading the page. See [react-router-starter](https://github.com/mozilla-neutrino/react-router-starter) for the complete code.

