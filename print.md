

# Data Fetching in React
- Team Treehouse
- Unit 7, Part 8, Workshop
- `t7-8_react-fetch_workshop.md`

## Topic Roster
- `useState`-hook
- Rendering
- Passing data down with `props`
- Passing data from child to parent

## Table of Contents
1. [Getting an API Key from Giphy](#getting-an-api-key-from-giphy) (collection, 7811)
1. [Fetching Data with the Fetch API](#fetching-data-with-the-fetch-api) (video, 7812)
1. [Fetching Data with Axios](#fetching-data-with-axios) (video, 7813)
1. [Displaying the Data](#displaying-the-data) (video, 7814)
1. [Building a Search Feature]() (video, 7815)
1. [Displaying the Search Results]() (video, 7816)



----------------------------------------------------------------------------------------------------
## Summary
- Learn about different methods for (fetching, displaying) external data in React
- Use the fetch API
- Use Axios get-method to perform a fetch-request
- Render data to site
- Use conditional rendering to add a loading indicator
- Use conditional rendering to display "Not Found" component when API returns no data



----------------------------------------------------------------------------------------------------
## Initial Code Dump

```html
<!--
./index.html -->


<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Vite + React</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
```

----------------------------------------------------------------------------------------------------
```jsx
// './src/main.jsx'

import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App.jsx';

const root = ReactDOM.createRoot(document.getElementById('root'));

root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
);
```

----------------------------------------------------------------------------------------------------
```jsx
// './src/App.jsx'

import React, {useEffect, useState} from "react";
import SearchForm from './Components/SearchForm.jsx';
import GifList from "./Components/GifList.jsx";

function App() {

   // Array of GIF-objects
   const [gifs, setGifs] = useState([]);

/*
----------------------------------------------------------------------------------------------------
### Data-Fetching ( External => useEffect() )
- Good location and method for:
   - loading external data when a Component gets mounted to the DOM
   - if request depends on another variable (e.g. a search-term)
- Allows us to control when the data is fetched
- Recall:
   - useEffect( setup(), [{dependencies}])
- fetch() uses promises to handle results
   - fetch() returns a `Promise`
   - Chain methods as sequential callbacks
   - Each callback ( .then() ) modifies the previous promise (e.g. convert to JSON)
   - Then returns a new promise of its own
*/

   // Fetch data immediately after Component-mounts => empty dependency-array
   // 1. fetch, 2. JSON, 3. Update `gifs`-state
   useEffect( ()=>{
      fetch('https://api.giphy.com/v1/gifs/trending?api_key={paste key here}&limit=24&rating=g')
         .then( res=>res.json() )
         .then( dat=>setGifs(dat.data) )
         .catch( err=>console.log('Error fetching/parsing data:', err));
   }, []);

   return (
      <div>
      <div className="main-header">
         <div className="inner">
            <h1 className="main-title">GifSearch</h1>
            <SearchForm />
         </div>
      </div>
      <div className="main-content">
         <GifList />
      </div>
      </div>
   );
}

export default App;
```

----------------------------------------------------------------------------------------------------
```jsx
// ./src/Components/SearchForm.jsx

import React, { useState } from 'react';

const SearchForm = props => {
  const [searchText, setSearchText] = useState('');

  const handleSubmit = e => {
    e.preventDefault();
    e.currentTarget.reset()
  }

  return (
    <form className="search-form" onSubmit={e => handleSubmit(e)} >
      <label className="is-hidden" htmlFor="search">Search</label>
      <input type="search" 
             onChange={e => setSearchText(e.target.value)}
             name="search" 
             placeholder="Search..." />
      <button type="submit" id="submit" className="search-button"><i className="material-icons icn-search">search</i></button>
    </form>      
  );
}

export default SearchForm;
```

----------------------------------------------------------------------------------------------------
```jsx
// ./src/Components/Gif.jsx

import React from 'react';

const Gif = props => (
  <li className="gif-wrap">
    <img alt=""/>
  </li>
);

export default Gif;
```

----------------------------------------------------------------------------------------------------
```jsx
// ./src/Components/NoGifs.jsx

import React from 'react';

const NoGifs = props => (
  <li className='no-gifs'>
    <i className="material-icons icon-gif">sentiment_very_dissatisfied</i>
    <h3>Sorry, no GIFs match your search.</h3>
  </li>
);

export default NoGifs;
```

----------------------------------------------------------------------------------------------------
```jsx
// ./src/Components/GifList.jsx

import React from 'react';
import Gif from './Gif.jsx';

const GifList = props => { 
  
  return(
    <ul className="gif-list">
      <Gif />
    </ul> 
  );
}

export default GifList;
```

----------------------------------------------------------------------------------------------------
## Getting an API Key from Giphy
- `tags` : tth, video, 7811

Intentionally left blank.

7811 [ToC](#table-of-contents) <br><br>



----------------------------------------------------------------------------------------------------
## Fetching Data with the Fetch API
- `tags` : tth, video, 7812

### `fetch()`-API
- Once data finished loading, fetch returns a response-object
   - res-obj contains response accessible in the .then() callbacks

- We will write all data-fetching code in `App.jsx` (standard pattern?)
   - Will pass external data to child-Components as `props`
- To load external data and update state in React use the following `Promise`-based approaches:
   - i.e. they use JS-promsies to handle server-responses
   - `Fetch API`: data-fetching interface native to browser
   - `Axios`: similar to `Fetch API`
- Trending GIFs Endpoint:
   ```
   https://api.giphy.com/v1/gifs/trending?api_key=<ENTER YOUR API KEY HERE>&limit=24&rating=g
   ```
- [ ] https://teamtreehouse.com/library/asynchronous-programming-with-javascript/what-is-a-promise


### Code Dump
```jsx
// './src/App.jsx'

import React, {useEffect, useState} from "react";
import SearchForm from './Components/SearchForm.jsx';
import GifList from "./Components/GifList.jsx";

...

   // Array of GIF-objects
   const [gifs, setGifs] = useState([]);

/*
----------------------------------------------------------------------------------------------------
### Data-Fetching ( External => useEffect() )
- Good location and method for:
   - loading external data when a Component gets mounted to the DOM
   - if request depends on another variable (e.g. a search-term)
- Allows us to control when the data is fetched
- Recall:
   - useEffect( setup(), [{dependencies}])
- fetch() uses promises to handle results
   - fetch() returns a `Promise`
   - Chain methods as sequential callbacks
   - Each callback ( .then() ) modifies the previous promise (e.g. convert to JSON)
   - Then returns a new promise of its own
*/

   // Fetch data immediately after Component-mounts => empty dependency-array
   // 1. fetch, 2. JSON, 3. Update `gifs`-state
   useEffect( ()=>{
      fetch('https://api.giphy.com/v1/gifs/trending?api_key={enter api key here}&limit=24&rating=g')
         .then( res=>res.json() )
         .then( dat=>setGifs(dat.data) )
         .catch( err=>console.log('Error fetching/parsing data:', err));
   }, []);
```

7812 [ToC](#table-of-contents) <br><br>



----------------------------------------------------------------------------------------------------
## Fetching Data with Axios
- `tags` : tth, video, 7813

### Environment
```
 $ npm i axios
```

### Summary
- `Axios`: Promise-based HTTP-client for browser, *and* Node.js
   - Not specific to `React`
   - Can be used with other JavaScript-(frameworks / libraries)
   - Can be used with Node.js
   - Automatic conversion to JSON
   - Supports `Promise`-API
   - <mark>Allows (req, res) intercept</mark>:
      - Allows modification before accessed by ( .then() / .catch() )
   - "Can post data to a server to protect app from `SXRF` attacks" (client-side protection against)
      - Prevent malicious requests and actions from being executed on app

### Code Store
```jsx
// './src/App.jsx'

import axios from 'axios';
...
const giphyUrl = 'https://api.giphy.com/v1/gifs/trending?api_key={enter api key here}&limit=24&rating=g';
...
// Fetch data immediately after Component-mounts => empty dependency-array
   // 1. fetch, 2. JSON, 3. Update `gifs`-state
   useEffect( ()=>{
      axios.get( giphyUrl )
         .then( res => setGifs(res.data.data) )
         .catch( err => console.log('Error fetching/parsing data:', err) );
   }, []);
```

### Links
- [Axios Documentation][#78131]
- [AXios vs fetch()][#78132]

[#78131]: https://axios-http.com/
[#78132]: https://github.com/axios/axios/issues/314

7813 [ToC](#table-of-contents) <br><br>



----------------------------------------------------------------------------------------------------
## Displaying the Data
- `tags` : tth, video, 7814

### Summary
- <u>Best Practice</u>: Main "App.jsx" file shares data *and* behavior down to children
   - Include data-fetching logic within a Container-component (e.g. App.jsx)
   - `Presentational-Components` (e.g. `GifList`, `Gif`) should *NOT* handle own data-fetching
      - Couples `data` to `view` too tightly
      - Allows for better reusability of presentational components
- `GifList` will receive data from `App` and *ONLY* be responsible for how the list of Gifs look

### Guide Notes
- Every time `var` is updated `Component` recieves `var`
   ```jsx
   <Component propA={var} />
   ```

### Code Store
- App.jsx
   ```jsx
   // './src/App.jsx'

   <GifList resData={gifs} />
   ```

- GifList.jsx
   ```jsx
   const GifList = ({ resData }) => {

      const data = resData;
      let gifs = data.map(gif => (
         <Gif
            key={gif.id}
            url={gif.images.fixed_height.url}
         />
      ));

      return (
         <ul className="gif-list">
            {gifs}
         </ul>
      );
   }
   ```

- Gif.jsx
   ```jsx
   const Gif = ({url}) => (
   <li className="gif-wrap">
      <img src={url} alt=""/>
   </li>
   );
   ```

7814 [ToC](#table-of-contents) <br><br>



----------------------------------------------------------------------------------------------------
## Building a Search Feature
- `tags` : tth, video, 7815

### Code Store

- App.jsx
   ```jsx
   ...
   function App() {

      // Array of GIF-objects
      const [gifs, setGifs] = useState([]);
      const [query, setQuery] = useState('');

      const handleQueryChange = searchText => setQuery(searchText);

      // Fetch data - Runs only 1x (upone mounting App to DOM) if no dependencies
      useEffect( ()=>{
         let activeFetch=true;
         const giphyUrl = `https://api.giphy.com/v1/gifs/search?q=${query}&api_key={enter api key here}&limit=12&rating=g`;

         axios.get( giphyUrl )
            .then( res => {
               if(activeFetch) setGifs(res.data.data);
            })
            .catch( err => console.log('Error fetching/parsing data:', err) );

         // ### Avoid "Race"-Condition
         // Return a clean-up function that always runs before the next setup function
         // - The cleanup-function-`false` either happens right before .then()
         //    - b/c another request was made quickly OR
         // - the gifs get set, then reset on a quick new-request
         return () => {activeFetch=false}
      }, [query]);  // Make request every time query changes

      return (
         <div>
         <div className="main-header">
            <div className="inner">
               <h1 className="main-title">GifSearch</h1>
               <SearchForm queryChange={handleQueryChange} />
            </div>
         </div>
         <div className="main-content">
            <GifList resData={gifs} />
         </div>
         </div>
      );
   }
   ```
- SearchForm.jsx
   ```jsx
   // ./src/Components/SearchForm.jsx

   import React, { useState } from 'react';

   const SearchForm = props => {
   const [searchText, setSearchText] = useState('');

   const handleSubmit = e => {
      e.preventDefault();
      props.queryChange(searchText);
      e.currentTarget.reset()
   }

   return (
      <form className="search-form" onSubmit={e => handleSubmit(e)} >
         <label className="is-hidden" htmlFor="search">Search</label>
         <input type="search" 
               onChange={e => setSearchText(e.target.value)}
               name="search" 
               placeholder="Search..." />
         <button type="submit" id="submit" className="search-button"><i className="material-icons icn-search">search</i></button>
      </form>      
   );
   }

   export default SearchForm;
   ```

- Gif.jsx
   ```jsx
   // ./src/Components/Gif.jsx

   import React from 'react';

   const Gif = ({url}) => (
   <li className="gif-wrap">
      <img src={url} alt=""/>
   </li>
   );

   export default Gif;
   ```

7815 [ToC](#table-of-contents) <br><br>



----------------------------------------------------------------------------------------------------
## Displaying the Search Results
- `tags` : tth, video, 7816

### Summary
- Use `refs` for direct access to DOM-elements
- Add default parameters to "query"-state
- Use conditional rendering to add a loading indicator

### Environment
- IO Fragment
   ```
   npm i @mui/material @emotion/react @emotion/styled @mui/icons-material
   ```

### Code Store
- <u>App.jsx</u>
   ```jsx
   // './src/App.jsx'

   import React, {useEffect, useState} from "react";
   import axios from 'axios';
   import SearchForm from './Components/SearchForm.jsx';
   import GifList from "./Components/GifList.jsx";

   function App() {

      // Array of GIF-objects
      const [gifs, setGifs] = useState([]);
      const [query, setQuery] = useState('candy');
      const [loading, setLoading] = useState(true);

      const handleQueryChange = searchText => setQuery(searchText);

      // Fetch data - Runs only 1x (upone mounting App to DOM) if no dependencies
      useEffect( ()=>{
         setLoading(true);
         let activeFetch=true;
         const giphyUrl = `https://api.giphy.com/v1/gifs/search?q=${query}&api_key={enter api key here}&limit=12&rating=g`;

         axios.get( giphyUrl )
            .then( res => {
               if(activeFetch) {
                  setGifs(res.data.data);
                  setLoading(false);
               }
            })
            .catch( err => console.log('Error fetching/parsing data:', err) );

         // ### Avoid "Race"-Condition
         // Return a clean-up function that always runs before the next setup function
         // - The cleanup-function-`false` either happens right before .then()
         //    - b/c another request was made quickly OR
         // - the gifs get set, then reset on a quick new-request
         return () => {activeFetch=false}
      }, [query]);  // Make request every time query changes

      return (
         <div>
         <div className="main-header">
            <div className="inner">
               <h1 className="main-title">GifSearch</h1>
               <SearchForm queryChange={handleQueryChange} />
            </div>
         </div>
         <div className="main-content">
            {
               (loading)
               ? <p>Loading ...</p>
               : <GifList resData={gifs} />
            }
         </div>
         </div>
      );
   }

   export default App;
   ```

- <u>SearchForm.jsx</u>
   ```jsx
   // ./src/Components/SearchForm.jsx

   import React, { useRef } from 'react';

   const SearchForm = props => {

      // DOM-Element Reference
      const searchText = useRef(null);

      const handleSubmit = e => {
         e.preventDefault();
         props.queryChange(searchText.current.value);
         e.currentTarget.reset()
      }

      return (
         <form className="search-form" onSubmit={e => handleSubmit(e)} >
            <label className="is-hidden" htmlFor="search">Search</label>
            <input type="search"
               ref={searchText}
               name="search"
               placeholder="Search..." />
            <button type="submit" id="submit" className="search-button"><i className="material-icons icn-search">search</i></button>
         </form>
      );
   }

   export default SearchForm;
   ```

- <u>GifList.jsx</u>
   ```jsx
   // ./src/Components/GifList.jsx
   ...
   import NoGifs from './NoGifs.jsx';
   ...
   const GifList = ({ resData }) => {

      const data = resData;
      let gifs = <NoGifs />

      if(data.length>0) gifs = data.map(gif => (
         <Gif
            key={gif.id}
            url={gif.images.fixed_height.url}
         />
      ));

      return ...
   ```

- <u>NoGifs.jsx</u>
   ```jsx
   // ./src/Components/NoGifs.jsx

   import React from 'react';
   import { SentimentVeryDissatisfied } from '@mui/icons-material';

   const NoGifs = props => (
      <li className='no-gifs'>
         <SentimentVeryDissatisfied className="material-icons icon-gif" />
         {/* <i className="material-icons icon-gif">sentiment_very_dissatisfied</i> */}
         <h3>Sorry, no GIFs match your search.</h3>
      </li>
   );

   export default NoGifs;
   ```
- <u>index.css</u>
   ```jsx
   svg.icon-gif {
      font-size: 12em;
   }
   ```

7816 [ToC](#table-of-contents) <br><br>

