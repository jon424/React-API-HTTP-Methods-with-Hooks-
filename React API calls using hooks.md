# React API/HTTP Methods (with Hooks)
## Installation Notes:
This example uses a react project called <b>hooks-api-example</b>. To create this project, open a new terminal and run:

```
npx create-react-app hooks-api-example
```
cd into the project and start it:
```
cd hooks-api-example
npm start
```

We will install the <b>axios</b> library to fetch data:
```
npm i axios
```

For this example, we will use the <b>App.js</b> file to access an API. The <b>App.js</b> component is rendered in it's parent: <b>index.js</b>. 
<br><br>
# GET Requests
We will use an API that contains information about breweries. Calling this API endpoint:
https://api.openbrewerydb.org/breweries
<br>...will yield JSON data that is structured as:
```
{
     {
        "id": 2,
        "name": "Avondale Brewing Co",
        "brewery_type": "micro",
        "street": "201 41st St S",
        "city": "Birmingham",
        "state": "Alabama",
        "postal_code": "35222-1932",
        "country": "United States",
        "longitude": "-86.774322",
        "latitude": "33.524521",
        "phone": "2057775456",
        "website_url": "http://www.avondalebrewing.com",
        "updated_at": "2018-08-23T23:19:57.825Z"
    },

    etc...
}
```
We can display the name, website link, and physical address of each brewery with the following code. Delete all of the code in the <b>App.js</b> file and replace it with:

```js
import React, { useState, useEffect } from "react";
import axios from "axios";

function App() {
  const [data, setData] = useState([]);

  useEffect(() => {
    axios
      .get("https://api.openbrewerydb.org/breweries")
      .then((res) => {
        setData(res.data);
      })
      .catch((err) => {
        console.log(err);
      });
  }, []);

  return (
    <ul>
      {data.map((item) => (
        <li key={item.id}>
          <a href={item.website_url}>{item.name}</a>
          <p>{item.street}</p>
          <p>{item.city}, {item.state} {item.postal_code}</p>
        </li>
      ))}
    </ul>
  );
}

export default App;
```

# GET Request With Search Query
Our example API allows for users to search by city. We can create a simple form that allows users to provide a city and return all breweries found in that city. We can also set up basic handling for errors and loading state:
```js
import React, { Fragment, useState, useEffect } from "react";
import axios from "axios";

function App() {
  const [data, setData] = useState([]);
  const [isError, setIsError] = useState(false);
  const [isLoading, setIsLoading] = useState(false);
  const [query, setQuery] = useState("Ann Arbor");
  const [url, setUrl] = useState(
    "https://api.openbrewerydb.org/breweries?by_city=ann_arbor"
  );

  useEffect(() => {
    const fetchData = async () => {
      setIsError(false);
      setIsLoading(true);

      try {
        const result = await axios(url);
        setData(result.data);
      } catch (error) {
        setIsError(true);
      }
      setIsLoading(false);
    };

    fetchData();
  }, [url]);

  return (
    <Fragment>
      <form
        onSubmit={(event) => {
          setUrl(`https://api.openbrewerydb.org/breweries?by_city=${query}`);

          event.preventDefault();
        }}
      >
        <input
          type="text"
          value={query}
          onChange={(event) => setQuery(event.target.value)}
        />
        <button type="submit">Search</button>
      </form>

      {isError && <div>Something went wrong...</div>}

      {isLoading ? (
        <div>Loading...</div>
      ) : (
        <ul>
          {data.map((item) => (
            <li key={item.id}>
              <a href={item.website_url}>{item.name}</a>
            </li>
          ))}
        </ul>
      )}
    </Fragment>
  );
}

export default App;
```















