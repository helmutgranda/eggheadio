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
  const allPockemon = ['pikachu', 'charizard', 'squirtle', 'balbasore', 'Eevee']

  allPokemon.forEach((pokemon, index) => {
    createPage({
      path: `/pokemons/${pokemon}`,
      component: require.resolve("./src/templates/pokemon.js"),
      context: {pokemon, index}
    })
  });

}
```

```
//src/templates/pockemon.js

import React from 'react'

export default({pageContext: {pokemon, index}}) => {
 console.log(pageContext)
 const {pokemon, index} = pageContext
 return <h1> Pokemon: {pokemon} is number {index}</h1>
}

```

Here we update index.js to list the Pokemon files

```
import React from 'react'
import {Router} from '@reach/router'
const IndexPage = () => <h1>Hello World!</h1>

<Router>
 <Pokemon path="/pokemons/:name" />
</Router>

export default IndexPage
```

Question: In gatsby-node. what happens if the list changes dynamically. 
Answer: You need to rebuild the site again, and that is why you decide if some need to be dynamic vs static. Here are why some tools like Netlify are great to help and rebuild your site.

We have seem how to create pages either manually or programmatically.

# How do we have dynamic routes?

One example would be an user dashboard. For that we need to always a base page and then anything that comes after that, we tell Gatsby to go to the page and read the routes there and render something else

```js
// /src/pages/user.js
import React from "react"
import { Router } from "@reach/router"

const Timeline = () => <h2> Dynamic Timeline</h2>
const Dashboard = () => <h2> Dynamic Dashboard</h2>

const UserPage = () => (
<>
 <h1> User page</h1>
  <Link to="/user/timeline">Timeline</Link>{' '}
  <Link to="/user/dashboard">Dashboard</Link>
 <Router>
  <Timeline path="/user/timeline"/>
  <Dashboard path="/user/dashboard"/>
 </Router>
 </>
)

export default UserPage
```

Updating gatsby-node

```js
//gatsby-node.js

export.onCreatePage = ({page, actions}) => {
 const { createPage } = actions
  if (page.path.match(/^\/user\)) {
   page.matchPath = '/user/*'
   createPage(page)
  }
}

exports.createPages = ({actions}) {
  const { createPage } = actions
  const allPockemon = ['pikachu', 'charizard', 'squirtle', 'balbasore', 'Eevee']

  allPokemon.forEach((pokemon, index) => {
    createPage({
      path: `/pokemons/${pokemon}`,
      component: require.resolve("./src/templates/pokemon.js"),
      context: {pokemon, index}
    })
  })
  
}
```

after starting the server we can now we can visit localhost:8000/user/

# Lets play with some data

```js
// /src/components/timeline.js
import React from "react"

const Timeline = () => {

 const [status, setStatus] = useState('')
 
 useEffect( () => {
  fetch ('https://k4d.dev/netlify/functions/timeline')
   .then(response => response.json() )
    .then(timelineData => setTimeline(timelinedata))
     .then(response => response.json())
      .then(timelineData => response.json())
       .then(timelineData => setTimeline(timelineData))
    }, [])
 }
 
 return (
  <div>
   <h1>Timeline</h1>
   <ul>
    {timeline.map(t => (
    <li key={t.id}>
     <div>
      <div>
       <img src={t.user.profileImageUrl} alt={t.user.name} />
      <div>
       <p>{t.user.name}</p>
       <p>@{t.user.screenName}</p>
      </div>
      </div>
      
      <div dangerouslySetInnerHTML={{__html: t.text }} />
      </div>
      </li>
    )
 )}
 </ul>
</div>
}

export default Timeline
```

We are importing Timeline component into user.js below

```js
// /src/pages/user.js
import React from "react"
import { Router } from "@reach/router"
imoprt Timeline from '../components/timeline'

const Dashboard = () => <h2> Dynamic Dashboard</h2>

const UserPage = () => (
<>
 <h1> User page</h1>
  <Link to="/user/timeline">Timeline</Link>{' '}
  <Link to="/user/dashboard">Dashboard</Link>
 <Router>
  <Timeline path="/user/timeline"/>
  <Dashboard path="/user/dashboard"/>
 </Router>
 </>
)

export default UserPage
```

There is a difference between createPage and createPages

createPages is when Gatsby starts fresh is going to create all the pages on your website, now everytime the createPage is called the hook `exports.createPage` is called by Gatsby. If we need to change something in the already created page we use `exports.createPage`

Brief overview of Authentication.
- Create a private route and will accept a component.
- if the user is not logged in, the user will go back to /user/login
 - that is simplifying the behavior
- using Netlify Khaled only needed about 50 lines of code to do validation. 


Just in case restart the server :).

## Interesting links

https://stackoverflow.com/questions/31354559/using-node-js-require-vs-es6-import-export

https://pokeapi.co/

https://pixelastic.github.io/pokemonorbigdata/

Validation:

https://github.com/khaledgarbaya/rubbergoose

LearnJamStack:

https://learnjamstack.com/

Twitter: https://twitter.com/khaled_garbaja

