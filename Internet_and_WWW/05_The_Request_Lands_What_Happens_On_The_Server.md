## Introduction

Let us pick up exactly where we left off.

Arjun typed a URL, his Browser parsed it, checked the Cache, triggered a DNS lookup to find the IP Address of the BBC's Server, performed the TCP Handshake to open a reliable connection, and sent a structured HTTP GET Request out onto the Internet.

That Request has now arrived.

It has traveled through routers and cables, crossed potentially thousands of kilometers, and has landed on a Computer sitting in a data center somewhere, a Computer that has been patiently waiting for exactly this kind of message.

That Computer is the Server.

This article covers what happens on the Server's side of the story, from the moment the Request arrives to the moment the Server sends everything back. Just like the last article walked through every step on Arjun's side, we are now going to walk through every step on the other side, in the same level of detail.

The technical specifics of what the Server sends back, and what that Response actually looks like, will come in the next article.

<!-- 
IMAGE: A wide illustration continuing the journey from the previous article. The left side (greyed out) shows Arjun's laptop with "Client Side - Done" marked on it. A glowing arrow points right toward a large data center building on the right side. Inside the building, lights are on. Caption: "The Request has arrived. Now what?" Style: flat vector, continuation of the same visual style from article 3, warm to cool color gradient from left to right.
-->

### The Scenario Continues

Remember Arjun? He pressed Enter on **www.bbc.co.uk/food/recipes/spaghetti** and is now staring at his screen, waiting for the recipe page to appear.

From his side, nothing is happening. Chrome shows a small loading indicator spinning in the tab. It looks uneventful.

But on the other end of that connection, inside a BBC data center, a Server has just received his Request and is about to do a significant amount of work in a very short time.

Let us follow the Request from the moment it arrives.

### Before We Begin: The Server Is Not One Thing

Here is something important to understand before diving into the steps.

In most real-world Websites, "the Server" is not a single Computer doing everything. It is actually a team of specialised Computers, each responsible for a specific part of handling the Request.

Think of it like a large, well-run kitchen. A restaurant does not have one person who takes the order, cooks the food, manages the pantry, and brings the dish to your table. There are different roles: a front-of-house person who takes your order, chefs who cook, and a stockroom for ingredients. Each one handles their part of getting food to your table.

Web architecture works the same way.

**The Web Server** is the front-of-house. It is the first Computer to receive any incoming Request. It listens for Requests around the clock, reads them, and decides what to do with them. If the answer is simple, it handles it directly. If it is more complex, it passes the work on.

**The Application Server** is the kitchen. This is where the actual logic and computation happens. When a Request needs thinking, like fetching a user's order history, calculating a search result, or building a personalised page, the Application Server is the one doing that work.

**The Database Server** is the pantry. It is a separate Computer dedicated entirely to storing and managing data. User accounts, product listings, articles, recipe content, anything that needs to be saved and retrieved lives here. When the Application Server needs data, it sends a query to the Database Server and waits for it to respond.

In smaller Websites, all three of these roles might run on a single Computer. In large-scale systems like BBC or Amazon, each role might be spread across hundreds of Computers. But the roles themselves are always the same.

<!-- 
IMAGE: A restaurant cross-section illustration. On the left is a dining area with a diner (Arjun) labeled "Client / User". A waiter labeled "Web Server" stands at the entrance, receiving an order slip. In the middle is an open kitchen labeled "Application Server" with chefs working. At the back is a large pantry labeled "Database Server" filled with ingredients. Arrows show the flow: Diner gives order to Waiter, Waiter passes to Kitchen, Kitchen retrieves from Pantry, Kitchen prepares dish, Waiter brings dish back to Diner. Style: warm flat vector illustration, clean and educational.
-->

### Let's Define Things Now...

> **Web Server:** The first Computer to receive an incoming HTTP Request. It handles simple requests directly and routes complex ones to the Application Server. Common examples include NGINX and Apache.

> **Application Server:** The Computer that runs the business logic of a Website. It processes complex requests, makes decisions, and coordinates with the Database to build a Response.

> **Database Server:** A dedicated Computer that stores and manages all of a Website's persistent data, such as user accounts, content, and records. It responds to queries from the Application Server.

> **Database:** An organised collection of structured data, stored on the Database Server, that can be searched, filtered, and retrieved using queries.

### The Request Arrives: Step by Step

Now let us walk through what actually happens after Arjun's HTTP Request lands.

#### Step 1: The Web Server Receives the Request

The Web Server is running a piece of software specifically designed to listen for incoming HTTP Requests. The moment Arjun's Request arrives, the Web Server accepts it and reads it.

The first things it looks at are the **Method** and the **Path.**

The Method tells the Server what kind of action is being requested. Arjun's Request has the Method **GET**, which means he is asking for something and not sending anything. The Path is **/food/recipes/spaghetti**, which tells the Server exactly which resource he wants.

At this point, the Web Server faces its first decision: can it answer this Request on its own, or does it need help?

#### Step 2: Static or Dynamic?

This is one of the most important decisions in the entire process.

Some content on a Website is **Static.** It does not change from one visitor to the next. A CSS file is the same for every person who visits the site. A logo image is the same. A JavaScript file is the same. The Web Server can serve these Files directly from its local storage without involving anyone else. It finds the File, attaches it to a Response, and sends it straight back.

Other content is **Dynamic.** It is different depending on who is asking, when they are asking, or what they searched for. A recipe page on the BBC might be the same for everyone, but a "My Recipes" page would be different for every logged-in user. A search results page changes based on what you searched. A product listing page on Amazon changes based on your location, your account history, and current stock levels.

Dynamic content cannot be served from a static File because it does not exist as a File. It has to be **built on the fly** by the Application Server, often using data retrieved from the Database.

For Arjun's recipe page, the Web Server determines that the content is dynamic. The recipe text and images are stored in a Database and need to be fetched and assembled. So it passes the Request to the Application Server.

<!-- 
IMAGE: A flowchart. Starting node: "Request Arrives at Web Server". Decision diamond: "Is the content Static or Dynamic?". Left branch (Static): arrow labeled "Yes, it is a File" goes directly to "Send File in Response." Right branch (Dynamic): arrow labeled "No, needs computation" goes to "Pass to Application Server." Style: clean white flowchart on a neutral background, two-color branches, minimal text.
-->

#### Step 3: The Application Server Takes Over

The Application Server receives the Request from the Web Server and begins processing it.

It reads the details of the Request and runs the logic needed to fulfil it. For the BBC recipe page, its job is roughly: "Find the spaghetti recipe content, retrieve the relevant images, get the author information, and prepare everything so it can be assembled into an HTML page."

To do any of that, it almost certainly needs to talk to the Database.

#### Step 4: Querying the Database

The Application Server sends a **Query** to the Database Server. A Query is a structured question, essentially a set of instructions telling the Database exactly what data to find and return.

For Arjun's request, the Query might be asking for: the recipe title, the list of ingredients, the cooking steps, the author's name, and the URLs of any images associated with the recipe.

The Database Server searches through its stored data, finds the matching records, and returns them to the Application Server.

Think of it as the chef shouting to the stockroom: "I need spaghetti, tomatoes, garlic, and olive oil." The stockroom person finds exactly those ingredients and passes them through. The chef does not need to know where in the stockroom they were kept. They just need the right ingredients delivered.

<!-- 
IMAGE: A zoomed-in view of just the Kitchen and Pantry from the earlier restaurant illustration. The chef (Application Server) holds a slip of paper labeled "Query: get spaghetti recipe data." An arrow goes from the chef to the pantry. The pantry person hands back a tray labeled "Recipe Data: title, ingredients, steps, images." Another arrow returns to the chef. Style: same warm flat vector, consistent with the restaurant illustration.
-->

#### Step 5: The Application Server Builds the Response

With the data back from the Database, the Application Server now has everything it needs.

It assembles all the pieces into a complete HTML Document. It takes the recipe title and wraps it in an HTML heading tag. It takes the list of ingredients and formats them as an HTML list. It takes the cooking steps and turns them into numbered paragraphs. It includes references to the CSS Files that will style the page and the JavaScript Files that will add interactivity.

The result is a complete, ready-to-send HTML File, built specifically for Arjun's Request, at the moment he asked for it.

This assembled HTML is handed back to the Web Server.

#### Step 6: The Web Server Sends the Response

The Web Server now has everything it needs to reply to Arjun.

It packages the assembled HTML, along with any other Files needed (CSS, JavaScript, images), into a structured HTTP Response and sends it back through the same connection that Arjun's Request came in on.

That Response travels back across the Network, through the same cables and routers, back to Arjun's device.

The loading spinner in his Chrome tab is about to stop.

### A Word on GET and POST

Before we move on, it is worth establishing the two most important HTTP Methods a bit more clearly, because the Server behaves differently depending on which one it receives.

**GET** means: "I want to receive something. Give me this resource."

When Arjun loads the recipe page, he is making a GET Request. He is not sending any data to the Server. He is just asking for a page. The Server reads the Request, finds or builds the content, and sends it back. Nothing on the Server changes as a result of a GET Request.

**POST** means: "I am sending you something. Do something with this data."

When you fill out a login form and click "Sign In," your Browser sends a POST Request. Your username and password travel in the Body of that Request. The Server receives it, validates the data, checks it against the Database, and responds based on whether it matched or not. Something on the Server side is triggered or changed as a result.

A helpful way to think about it: GET is like reading a menu. POST is like placing an order. Reading the menu does not change anything in the kitchen. Placing an order does.

<!-- 
IMAGE: Two side-by-side panels. Left panel labeled "GET Request": a diner reading a menu, nothing happening in the kitchen. The label below reads "Asking for something. Nothing changes on the server." Right panel labeled "POST Request": a diner handing an order slip to a waiter, the kitchen starting to work. The label below reads "Sending something. The server acts on the data." Style: flat vector, consistent restaurant illustration style.
-->

### Let's Define Things Now...

> **Static Content:** Files that are identical for every User and every Request. They are served directly by the Web Server without any processing (HTML Files, CSS Files, images, fonts).

> **Dynamic Content:** Content that is generated on the fly by the Application Server, often using data from the Database, and may differ from one User or Request to the next.

> **Query:** A structured request sent from the Application Server to the Database Server, asking it to find, filter, or return specific data.

> **GET:** An HTTP Method that means the Client is requesting a resource from the Server. No data is sent. Nothing on the Server changes.

> **POST:** An HTTP Method that means the Client is sending data to the Server for it to process, store, or act on.

### Activity: Role Play

For each of the five User actions below, do two things:

1. Label it as a **GET** or **POST** Request.
2. Write one sentence describing what the Server probably does when it receives that Request.

**Actions:**

1. Loading the homepage of a news Website.
2. Submitting a login form with your email and password.
3. Searching for "spaghetti recipe" in a Website's search bar.
4. Uploading a profile photo on a social media platform.
5. Clicking "Log Out" on a Website.

Think through the logic for each one. Is the User asking the Server for something, or sending the Server something to act on? What would the Server need to do as a result?

## What's Coming Next...

We have now walked through the full journey from both sides.

Arjun's Browser built a Request and sent it. The Server received it, decided how to handle it, queried the Database, assembled a complete HTML Response, and sent it back.

But what does that HTTP Response actually look like? What is inside it? How does the Server communicate success, failure, or redirection? And what are Status Codes?

The next article goes under the hood on the HTTP Response, breaking it apart the same way we broke apart the HTTP Request, so you can read and understand every part of it.
