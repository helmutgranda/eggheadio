# [LIVE! truck Migrating from Create-React-App to Gatsby with Khaled Garbaya [Free Community Event]](https://community.egghead.io/t/live-migrating-from-create-react-app-to-gatsby-with-khaled-garbaya-free-community-event/1697)

* Explaining Gatsby - basics without installing Gatsby.
* Show some concepts 
  * How to create a few static pages
  * How to create them dynamicaly
* When do you create static pages and when you use routes
* Build a small app
* Dig deeper how to use plugins

* What does it take to create a project?
[ commands below need to be run commands: 

```
$ mkdir simple-gatsby
$ cd simple-gatsby
$ npm init -y # uses the deault data
# Open in our own code editor like VSCode
```

Gatsby uses react but does not ship with React, so we can use different versions of React as needed, the commands below will install the dependencies needed

```
$ npm i gatsby react react-dom
```

Question: Is gatsby mainly for blogs and ecommerce?
- No really, you can over engineer with any framework, it scales with you and allows you to optimize specially when you are going to deploy to the web. The server-side rendering is a big win for us.

Question: Are you aware of any plugin for CMS?
- There are several plugins for different CMS systems such as Contentful (Created by Khaled :)) or WordPress.

We can remove the tests from scripts but we should add some of the following

```
"scripts" : {
"develop" "gatsby develop",
"build": "gatsby build",
"clean": "gatsby clean"
}
```


We now create a `src` and `pages` folder inside the src folder. inside the `pages` folder we create `index.js` which will be our entry point.

```
//index.js

import React from 'react'
const IndexPage = () => <h1>Hello world</h1>

export default IndexPage
```

Now we can run the following command to build our site

```
$ gatsby develop
```

Gatsby offers two things, by default http://localhost: 8000/

Once we are ready for production we run

```
$ gatsby build
```

When we build the project, a public folder is created and that is what we need to publish to the web.

## Adding more pages

Simply what we do is go to `pages` folder and add something like `about.js`

```
//about.js

import React from 'react'
const AboutPage = () => <h1>About me!</h1>

export default AboutPage
```

We need to run again the following code in order to run the server

```
$ gatsby develop
```

You can also give a random page to your site for example `http://localhost:8000/abcd` and will give you a nice 404 with a link to all the pages you have created.

In our app we have pages that are hardcoded that never change, but sometimes we need to create a lot of pages. Example 1000 blog posts. What we need to do is use gatsby-create-page api.

We create a file called gatsby-node.js, this will signal Gatsby to let it know that we want to enhance its functionality. In gatsby-node.js we interfere with the framework on server side. 

Below we will create a file to create dynamic pages using some Pokemon names.

```js
//gatsby-node.js
exports.createPages = ({actions}) {
  const {createPage} = actions
  const allPockemon = ["pikachu", "charizard", "squirtle"]

  allPokemon.forEach(pokemon => {
    createPage({
      path: `/pokemons/${pokemon}`,
      component: require.resolve("./src/templates/pokemon.js"),
    })
  });

}
```
