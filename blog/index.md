---
layout: layouts/blog.njk
title: Build a real estate video generator using Notion API + Shotstack API
name: Build a real estate video generator using Notion API + Shotstack API
type: content
description: Generate custom videos for several real estate housing.
date: 2022-11-23
author: Femi-ige Muyiwa
thumbnail:
Tags:
    - How to
    - Learn
related:
---

Owning real estate can be a hassle as you must simultaneously be available to several clients. Of course, you have to answer some questions about the property available, which alone can be daunting. A video description can save you time and money in this case. Still, the conventional way of making videos takes more time because you will have to create custom videos for each property in your catalog.

Do not fret! Using the Shotstack API, you can generate the required videos without custom-making each to suit each property. Then this is a tutorial for you as it entails using the Shotstack and the Notion API to generate a real estate video for different properties using their street address.

### Shotstack and Notion setup

#### Shotstack API and SDK

[Shotstack](https://dashboard.shotstack.io/register) provides a cloud-based [video editing API](https://shotstack.io/product/video-editing-api/) that uses JSON data to describe the arrangement of a video edit.

To get your Shotstack API key, register for a free [Shotstack developer account](https://dashboard.shotstack.io/register?utm_source=website&utm_medium=tutorial&utm_campaign=2020_11_tutorial_helloworld) and log in to get the API key. Shotstack provides two API keys, one for the staging environment and the other for production. For this tutorial, you will only need to copy the API key for staging, and you are good to go.

![Shotstack APi key](https://lh5.googleusercontent.com/-GUWvBy6b3Jw3-tlvBBH-uv0soRckaNWtN4lEVH9gFOw6TVCpo3DgmHKHNTHopP3tJ0NIOhS2pvTTLEFMeJ_HpHTnaZnCl-Y5_TuWjwBIkfTFEYyMGLiosZ_TdnoG2B60Vs9_HOLRj0V1t5m9FAdmnRiOVX0biPJxnd2VS1yqHjrvhIlfyxIPG8a3ZGJHA)

#### Notion

Next, you also need to store some sample data about the property information in a database. That is where Notion comes in, as It is a platform that provides a way to get information centralized and make it available to an end user using a link  (via the internet) or integrated into applications using an API endpoint.


To begin, create a Notion [account](https://www.notion.so/signup) or [login](https://www.notion.so/login) if you have an account. Then, head over to [Notion integrations](https://www.notion.so/my-integrations) as you will use it to expose your databases in your workspace to the Notion API. When creating the integration, specify the rules and permissions that determine the level of access Notion API has to the data stored on your database. Once you click on view integrations, you will see your Notion API Token.

![notion integration](https://paper-attachments.dropboxusercontent.com/s_7DDC7C5759BB5FABAFC4ECF2AA8E66ECDFD8DD7CAE8E6A4806D29281AED8C92A_1669189877815_Screenshot+114.png)
![notion integration peermission](https://lh6.googleusercontent.com/LE2K3gdwLdwKXlWxPrIXSIbeXWqtjrRULOZipq05cF1MUfflRuTCx4ra4cI2JgY3fFnJSGNRRMMiQVc37-fhx9rxDnEijPUyTsVbiPioHzaspktw4LR-B9Dlq0Ohhj05wm62ok75E3ls4TBFka9c8YoIZUPd37sVDxm5q0--akBe802mC69Zy9NOjccAIA)
![Notion API key](https://lh3.googleusercontent.com/8864EshyOqMZ_1BfqNqJlpFkGnaxMwQSLKyp5iiQCjBKni5nVjCmaX3YjsGHwl6o0B2gTKSS0JvLm5pjMYJU4fUJbCP0lLOgZlWv-Nw7xY85Fi0uyPBaIo0ykYWqT6dR186SBN6GMwyIfjN7vLt09YFDh3LopQHzTaFRhSfbk1-pNEGB2Us48uira-7cig)

Now you need to set up a database in Notion, and to do that, click *Add a page* on the homepage. Then set up a database table and copy the details in this [template](https://www.notion.so/f679aff84afb468e884d3b8ed2a09af4?v=916405422c4c49be80ffee30f6f51360) to the table. Then click the button on the top right, scroll to *Add connections* and specify your integration as the connection.

> Note: Change the **Type** for `number of bedrooms`, `postcode`, `car spaces`, `number of bathrooms`, and `Street Address` from **Number** to  **Text**.

### Getting started

This project is a full-stack application built using `Node.js` and `express` for the backend. To proceed, you will need to expose your endpoints so the front end can call them to render videos using the data stored in our database table. A front end can suffice for the task, but we will use a [query string](https://en.wikipedia.org/wiki/Query_string) to send values to our backend.

Thus, start by creating a new directory and changing to that directory by typing the following commands in the terminal:

```bash
mkdir <directory-name>
```

```bash
cd <directory-name>
```

> Note: change **directory-name** to the name of your choice

Next, type in the command below within the terminal to initialize the `Node.js` app and create a `package.json` file:

```bash
npm init -y
```

Next, you need to install the `Express package` to create a `Node server` , the `Axios package` for external API calls, and `Dotenv` to manage environment variables in our application. Use the following command to install the packages above:

```bash
npm install express axios dotenv
```

#### The data

As mentioned earlier, Shotstack enables you to prepare JSON template data beforehand to specify how you should render a video. [Here](shorturl.at/hnqFL) is a link to the JSON template utilized in this tutorial. To use the template, create a file called `data.js` and paste in the following code:

```javascript
exports.template = {
    "timeline": {
        "soundtrack": {
            "src": "https://shotstack-assets.s3.ap-southeast-2.amazonaws.com/music/unminus/ambisax.mp3",
            "effect": "fadeOut"
        },
        "background": "#000000",
        "tracks": [
            {
                "clips": [
                    {
                        "asset": {
                            "type": "html",
                            "html": "<p data-html-type=\"text\">{{street_address}}</p>",
                            "css": "p { color: #f0c20c; font-size: 40px; font-family: Roboto Black; text-align: left; }",
                            "width": 353,
                            "height": 141,
                            "position": "bottom"
                        },
                        "start": 1.2,
                        "length": 4.2,
                        "position": "center",
                        "offset": {
                            "x": -0.285,
                            "y": 0.192
                        },
                        "transition": {
                            "in": "slideRight",
                            "out": "slideLeft"
                        },
                        "fit": "none",
                        "scale": 1
                    },
                    {
                        "asset": {
                            "type": "html",
                            "html": "<p data-html-type=\"text\">{{postcode}}</p>",
                            "css": "p { color: #ffffff; font-size: 22px; font-family: Roboto Black; text-align: left; }",
                            "width": 320,
                            "height": 66,
                            "position": "bottom"
                        },
                        "start": 1.3,
                        "length": 3.9,
                        "position": "center",
                        "offset": {
                            "x": -0.3,
                            "y": 0.034
                        },
                        "transition": {
                            "in": "slideRight",
                            "out": "slideLeft"
                        },
                        "fit": "none",
                        "scale": 1
                    },
                    {
                        "asset": {
                            "type": "html",
                            "html": "<p data-html-type=\"text\">{{property_type}}</p>",
                            "css": "p { color: #ffffff; font-size: 17px; font-family: Roboto Black; text-align: left; }",
                            "width": 320,
                            "height": 32,
                            "position": "center"
                        },
                        "start": 1.4,
                        "length": 4,
                        "position": "center",
                        "offset": {
                            "x": -0.3,
                            "y": -0.122
                        },
                        "transition": {
                            "in": "fade",
                            "out": "fade"
                        },
                        "fit": "none",
                        "scale": 1
                    }
                ]
            },
            {
                "clips": [
                    {
                        "asset": {
                            "type": "image",
                            "src": "https://shotstack-assets.s3-ap-southeast-2.amazonaws.com/templates/real-estate-slideshow/bed.png"
                        },
                        "start": 1.4,
                        "length": 4,
                        "position": "center",
                        "offset": {
                            "x": -0.445,
                            "y": -0.054
                        },
                        "transition": {
                            "in": "fade",
                            "out": "fade"
                        },
                        "scale": 1,
                        "fit": "none"
                    },
                    {
                        "asset": {
                            "type": "image",
                            "src": "https://shotstack-assets.s3-ap-southeast-2.amazonaws.com/templates/real-estate-slideshow/bath.png"
                        },
                        "start": 1.4,
                        "length": 4,
                        "position": "center",
                        "offset": {
                            "x": -0.368,
                            "y": -0.054
                        },
                        "transition": {
                            "in": "fade",
                            "out": "fade"
                        },
                        "scale": 1,
                        "fit": "none"
                    },
                    {
                        "asset": {
                            "type": "image",
                            "src": "https://shotstack-assets.s3-ap-southeast-2.amazonaws.com/templates/real-estate-slideshow/car.png"
                        },
                        "start": 1.4,
                        "length": 4,
                        "offset": {
                            "x": -0.286,
                            "y": -0.054
                        },
                        "transition": {
                            "in": "fade",
                            "out": "fade"
                        },
                        "position": "center",
                        "scale": 1,
                        "fit": "none"
                    },
                    {
                        "asset": {
                            "type": "html",
                            "html": "<p data-html-type=\"text\">{{Bedroom_count}}</p>",
                            "css": "p { color: #ffffff; font-size: 18px; font-family: Roboto Black; text-align: left; }",
                            "width": 36,
                            "height": 26,
                            "position": "center"
                        },
                        "start": 1.4,
                        "length": 4,
                        "position": "center",
                        "offset": {
                            "y": -0.055,
                            "x": -0.405
                        },
                        "transition": {
                            "in": "fade",
                            "out": "fade"
                        },
                        "fit": "none",
                        "scale": 1
                    },
                    {
                        "asset": {
                            "type": "html",
                            "html": "<p data-html-type=\"text\">{{bathroom_count}}</p>",
                            "css": "p { color: #ffffff; font-size: 18px; font-family: Roboto Black; text-align: left; }",
                            "width": 36,
                            "height": 26,
                            "position": "center"
                        },
                        "start": 1.4,
                        "length": 4,
                        "position": "center",
                        "offset": {
                            "y": -0.055,
                            "x": -0.327
                        },
                        "transition": {
                            "in": "fade",
                            "out": "fade"
                        },
                        "fit": "none",
                        "scale": 1
                    },
                    {
                        "asset": {
                            "type": "html",
                            "html": "<p data-html-type=\"text\">{{garage_space}}</p>",
                            "css": "p { color: #ffffff; font-size: 18px; font-family: Roboto Black; text-align: left; }",
                            "width": 36,
                            "height": 26,
                            "position": "center"
                        },
                        "start": 1.4,
                        "length": 4,
                        "position": "center",
                        "offset": {
                            "y": -0.055,
                            "x": -0.246
                        },
                        "transition": {
                            "in": "fade",
                            "out": "fade"
                        },
                        "fit": "none",
                        "scale": 1
                    }
                ]
            },
            {
                "clips": [
                    {
                        "asset": {
                            "type": "image",
                            "src": "https://shotstack-assets.s3-ap-southeast-2.amazonaws.com/templates/real-estate-slideshow/content-left.png"
                        },
                        "start": 0,
                        "offset": {
                            "x": -0.252,
                            "y": 0
                        },
                        "position": "center",
                        "transition": {
                            "in": "carouselRight",
                            "out": "carouselLeft"
                        },
                        "length": 5.6,
                        "scale": 1,
                        "fit": "crop"
                    }
                ]
            },
            {
                "clips": [
                    {
                        "asset": {
                            "type": "image",
                            "src": "{{image_1}}"
                        },
                        "start": 0,
                        "length": 6,
                        "effect": "zoomInSlow",
                        "transition": {
                            "in": "fade",
                            "out": "fade"
                        },
                        "offset": {
                            "x": 0,
                            "y": 0
                        },
                        "position": "center",
                        "fit": "crop",
                        "scale": 1
                    },
                    {
                        "asset": {
                            "type": "image",
                            "src": "{{image_2}}"
                        },
                        "effect": "slideLeftSlow",
                        "start": 5,
                        "length": 7,
                        "offset": {
                            "x": 0,
                            "y": 0
                        },
                        "position": "center",
                        "transition": {
                            "in": "fade",
                            "out": "fade"
                        }
                    },
                    {
                        "asset": {
                            "type": "image",
                            "src": "{{image_3}}"
                        },
                        "effect": "slideRightSlow",
                        "length": 7,
                        "transition": {
                            "in": "fade",
                            "out": "fade"
                        },
                        "offset": {
                            "x": 0,
                            "y": 0
                        },
                        "position": "center",
                        "start": 11
                    },
                    {
                        "asset": {
                            "type": "image",
                            "src": "{{image_4}}"
                        },
                        "effect": "slideUpSlow",
                        "start": 17,
                        "length": 7,
                        "transition": {
                            "in": "fade",
                            "out": "fade"
                        }
                    }
                ]
            }
        ]
    },
    "output": {
        "format": "mp4",
        "size": {
            "width": 1024,
            "height": 576
        }
    }
}
```

The template data above contains placeholders you can replace with dynamically generated data. In this case, it will be the data from our Notion database created earlier.

To do this, start by creating another file called `function.js` and within that file, add the code below:

```javascript

const { template } = require("./data");

exports.extractMergeFields = (responseResult) => {
    console.log(responseResult);
    const merger_array = responseResult.map((elem, ind) => {
        const merger = {}
        merger.id = ind + 1;
        merger.street_address = elem?.properties['Street Address']?.title[0]?.plain_text;
        merger.bathroom_count = elem?.properties["number of bathrooms"]?.rich_text[0]?.plain_text;
        merger.Bedroom_count = elem?.properties["number of bedrooms"]?.rich_text[0]?.plain_text;
        merger.property_type = elem?.properties["property type"]?.rich_text[0]?.plain_text;
        merger.garage_space = elem?.properties["car spaces"]?.rich_text[0]?.plain_text;
        merger.image_1 = elem?.properties["property image 1"]?.url;
        merger.image_2 = elem?.properties["property image 2"]?.url;
        merger.image_3 = elem?.properties["property image 3"]?.url;
        merger.image_4 = elem?.properties["property image 4"]?.url;
        merger.postcode = elem?.properties["postcode"]?.rich_text[0]?.plain_text;
        merger.date = elem?.properties["Date"]?.date;
        merger.url = elem?.properties["url"];
        return merger;
    });
    return merger_array;
}


exports.get_merge_object = (street_address, merge_array) => {
    const arr = [];
    const temp_value = merge_array.find(element => (element?.street_address.trim().toLowerCase() == street_address?.trim().toLowerCase()));
    Object.keys(temp_value).forEach(elem => {
        arr.push({
            "find": elem,
            "replace": temp_value[elem]
        })
    })
    // filtering out irrelevant fields
    const placeholder_fields = ['street_address', 'postcode', 'property_type', 'Bedroom_count', 'bathroom_count', 'garage_space', 'image_1', 'image_2', 'image_3', 'image_4']
    return (arr.filter(elem => (
        placeholder_fields.includes(elem.find)
    )));
}


exports.template_data = (merge_array) => {
    return {
        "merge": [...merge_array],
        ...template
    }
}   
```


In the code above, there is a function called `extractmergeField`. You will use the function to extract the required properties and values from the Database needed for your JSON template.

Also, there is another function called `get_merge_object` . This function will use the database value to return a merged array of values to replace the placeholders in our template JSON file.

#### The code

Now, create a file (`index.js`) to serve as the entry point of your application using the code:

```javascript
const express = require('express');
require("dotenv").config();
const app = express();
const e = require('express');

const port = 3000;

app.listen(port, () => {
    console.log(`App listening on port ${port}`)
})
```

The code above creates an express server which you can run using the command.

```bash
node index.js
```

Next, create an environment file to store the API keys (Shotstack and Notion) created earlier.

```.env
NOTION_TOKEN = 'secret_*****************************'
SHOTSTACK_API_KEY = 'gP9****************************'
```

Also, create a file (`config.js`) to hold the configurations for our application and add the following code to the file:

``` javascript
exports.notion_base_url = 'https://api.notion.com/';
exports.shotstack_base_url = 'https://api.shotstack.io/';
exports.version = 'stage';
```


Back to the `index.js` file created earlier, at the moment, it is a pointless server at the moment. To put the server to use, create two endpoints; `/process-video` and `/query-rendering-status`. The first endpoint, `/process-video` will use two query parameters, `database_name` (Notion database name) and `street_address` (street address from template), which will return an `id` if you correctly input the details. Then, you will use the generated `id` to query the status in the `/query-rendering-status` endpoint, creating the URL for your real estate video.

``` javascript
const express = require('express');
require("dotenv").config();
const app = express();
const e = require('express');
const { queryShotstackStatus, renderVideo } = require("./handler");
const port = 3000;

app.get('/process-video', async (req, res) => {
    await renderVideo(req, res)
});

app.get('/query-rendering-status', async (req, res) => {
    await queryShotstackStatus(req, res, req.query.render_id)
});

app.listen(port, () => {
    console.log(`App listening on port ${port}`)
})
```

In the code above, we specified some function calls when we query the endpoint. Thus, start by creating another file called `handler.js,` and call the `renderVideo` function while passing in the request and the response objects. Following the authentication procedure to call the Notion API, we will request a search of all the databases of the utilized API token. Then, extract the database ID of the database that matches the query parameter passed in the request. If a database name match exists, we query the database query endpoint to fetch the properties of the database and their values.

Once the merge array of values is pre-pended to our JSON template, we are ready to process our video file. Shotstack provides us with a render assets API endpoint. When called uses the authentication we establish with our secret_key to render videos using the Shotstack video rendering cloud platform. Our application calls the `shotstackRequest` function, passing in the merged template JSON file. The request returns a string response of the status of the video rendering. We get a  "queuing" response for a started rendering process alongside an ID which we can use to query and test the status of the video generated.


Querying the GET query-rendering-status endpoint in our application while passing the rendering ID will call the "queryShotstackStatus" function. After completing the video rendering, our app returns the URL to the specified endpoint.

Here is the implementation below:

```javascript

const axios = require("axios");
const { version, shotstack_base_url, notion_base_url } = require("./config");
const { template_data } = require("./functions");
const { extractMergeFields, get_merge_object } = require("./functions");

const shotstackRequest = (req, res, merge_array) => {
    try {
        const headers = {
            'Content-Type': 'application/json',
            'Accept': 'application/json',
            'x-api-key': `${process.env.SHOTSTACK_API_KEY}`
        };
        axios.post(`${shotstack_base_url}${version}/render`, JSON.stringify(merge_array), {
            headers: headers
        })
            .then(async (response) => {
                return res.status(200).json({ data: response.data, msg: 'data fetched', status: "success" });
            })
            .catch((error) => {
                console.log(error);
                return res.status(400).json({ data: 'Something went wrong', msg: 'data fetched', status: "failed"});
            });
    } catch (error) {
        console.log(error);
        return res.status(400).json({ msg: 'Something went wrong', status: "failed" });
    }
}

exports.renderVideo = (req, res) => {
    try {
        (async () => {
            const headers = {
                'Content-Type': 'application/json',
                'Accept': 'application/json',
                'Authorization': `Bearer ${process.env.NOTION_TOKEN}`,
                'Notion-Version': "2022-06-28"
            };
            const body_data = {
                "filter": {
                    "value": "database",
                    "property": "object"
                }
            }
            // Searching for list of databases belonging to the used API token
            axios.post(`${notion_base_url}v1/search`, body_data, {
                headers: headers
            }).then(async (response) => {

                // extracting ID of the database that matches the database name passed alongside the request
                let database_id = response.data.results.find(elem => (
                    (elem.title.find(item => (item.plain_text == req.query.database_name)))
                ))?.id;

                // checking if database exists
                if (database_id !== undefined) {
                    axios.post(`${notion_base_url}v1/databases/${database_id}/query`, {}, {
                        headers: headers
                    }).then(async (response) => {

                        const merge_array = await extractMergeFields(response.data.results);

                        // returning database query if specific row is not selected
                        if (req.query.street_address === undefined) return res.status(400).json({ data: merge_array, msg: 'Select a street address and call this endpoint again with the value for the street address', status: "success"});

                        const merge_object = await get_merge_object(req.query.street_address, merge_array);

                        // checking if street address exist in database
                        if (merge_object == undefined) return res.status(400).json({ data: 'error', msg: 'No match for street address', status: "success"});

                        // rendering video using database properties
                        else return await shotstackRequest(req, res, template_data(merge_object));

                    }).catch((error) => {
                        console.log(error);
                        return res.status(400).json({ data: {}, msg: 'No data fetched', status: "failed" });
                    })
                } else {
                    return res.status(400).json({ data: 'error', msg: 'No such database found', status: "success"});
                }
            }).catch((error) => {
                console.log(error);
                return res.status(400).json({ data: {}, msg: 'No data fetched', status: "failed" });
            })
        })();
    } catch (error) {
        console.log(error);
        return res.status(400).json({ data: database_id, msg: 'data fetched', status: "success" });
    }
}


exports.queryShotstackStatus = (req, res, render_id) => {
    try {
        const headers = {
            'Content-Type': 'application/json',
            'Accept': 'application/json',
            'x-api-key': `${process.env.SHOTSTACK_API_KEY}`
        };
        axios.get(`${shotstack_base_url}${version}/render/${render_id}`, {
            headers: headers
        })
            .then(async (response) => {
                if (response.data?.response?.status !== 'done') {
                    return res.status(200).json({ data: { status: response.data?.response?.status }, msg: 'data fetched', status: "success" });
                } else {
                    return res.status(200).json({
                        data:
                        {
                            status: response.data?.response?.status,
                            url: response.data?.response?.url,
                            render_time: response.data?.response?.renderTime
                        }, msg: 'data fetched', status: "success"
                    });
                }
            })
            .catch((error) => {
                console.log(error);
                return res.status(400).json({ msg: 'data fetched', status: "failed" });
            });
    } catch (error) {
        console.log(error);
        return res.status(400).json({ msg: 'Something went wrong', status: "failed" });
    }
}
```

#### Result
https://youtu.be/0av9yMumhS4

> Note: if, for some reason, you are getting this error `TypeError: Cannot read properties of undefined (reading 'find')` when you run the application. Copy the content of the `package-lock.js` file in the GitHub repository into your current `package-lock.json` file, then run `npm install`.

### Conclusion

This article focused on a direct approach to building a real estate video generator. It discusses database + placeholder(JSON) + video Editing API = result(URL). Combining this article with a front end allows easy automation of videos to satisfy each customer's choice.

We hope this article has been of help and will help you serve future products regarding video editing. Create and share with us!
