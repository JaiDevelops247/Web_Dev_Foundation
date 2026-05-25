## Introduction

We now know what the Internet is, what the World Wide Web is, and what a Website is made of. Three Files: HTML, CSS, and JavaScript, sitting on a Computer called a Server, waiting to be delivered to anyone who asks for them.

But how does that asking actually happen?

For the next two articles, we are going to trace the complete journey of a single Web request, from the moment a person types something into a Browser all the way to the moment a finished page appears on their screen. This journey is one of the most fascinating things that happens in modern technology, and it happens in milliseconds, every single time you visit a Website.

This article covers the first half of that journey: what happens on **your side**, from the moment you type a URL to the moment your request leaves your device and heads out onto the Internet.

The next article will pick up from there and cover the technical machinery that takes over once your request is out in the world.

<!-- 
IMAGE: A wide horizontal illustration showing a journey broken into two halves. The left half shows a person at a laptop, a browser window, and arrows flowing outward from the device. The right half (greyed out, labeled "Coming Next") shows routers, a DNS server, and a web server. A dotted line divides the two halves. Caption: "The journey of a single web request." Style: flat vector, clean educational infographic style.
-->


It is a Sunday afternoon. Arjun is at his desk with his laptop, craving something different for dinner. He opens Chrome, types **www.bbc.co.uk/food/recipes/spaghetti** into the address bar at the top, and presses Enter.

About a second later, a beautifully styled recipe page appears on his screen. There are images of spaghetti, a list of ingredients, and step-by-step instructions.

Arjun does not think twice about it. To him, it just worked.

But an enormous amount happened in that one second. His simple action of pressing Enter triggered a precisely choreographed sequence of events involving his Browser, his Operating System, his Router, undersea cables, a Domain Name System, a remote Server, and finally his Browser again to render everything he sees.

Let us walk through it, step by step, starting from Arjun's side of the story.

<!-- 
IMAGE: Arjun sitting at a desk with a laptop open to a browser. The address bar shows "www.bbc.co.uk/food/recipes/spaghetti". A single glowing arrow flows from his laptop outward into a network of connected nodes, visualizing the request leaving the device. The background is a cosy room. Style: flat vector, warm tones, friendly and relatable.
-->

### Before We Begin: Establishing the Cast

This story has several key players. Let us introduce them properly before diving in, because you will see these terms constantly throughout this course and in the wider world of Web Development.

**The User**

The User is the human being interacting with the Browser. In our story, that is Arjun. Every Website in the world is ultimately built to serve the User, the person on the other side of the screen with a question, a task, or a goal.

**The Client**

In the world of Web Development, when two Computers communicate with each other, one of them is asking for something and the other is providing it. The one doing the asking is called the **Client.** In Arjun's case, the Client is his Browser, specifically Google Chrome running on his laptop. The Browser is doing the asking on behalf of the User.

The word "Client" comes from the real-world meaning: a client is someone who comes to a business to receive a service. The Browser is the client that comes to a Server to receive a Website.

**The Server**

The **Server** is the Computer on the other side. It is always on, always listening, and when it receives a request, it responds by sending back the requested Files. The BBC has Servers somewhere that hold all of their recipe Files. When Arjun's Browser asks for the spaghetti recipe page, one of those Servers sends it back.

**The Request**

The **Request** is the formal message that the Client (Browser) sends to the Server to ask for something. It is not just a casual ask. It is a structured message written in a specific format, containing specific information, sent using agreed-upon rules. We will look at what a Request looks like in detail shortly.

**The Response**

The **Response** is what the Server sends back after receiving the Request. It contains the Files Arjun asked for: the HTML, CSS, and JavaScript that will build the recipe page on his screen.

<!-- 
IMAGE: A four-character illustration in a row, like a cast lineup. Character 1: a person icon labeled "User (Arjun)". Character 2: a browser window icon labeled "Client (Chrome)". Character 3: a server tower icon labeled "Server (BBC's Computer)". Character 4: two arrows facing opposite directions, one labeled "Request" (going right) and one labeled "Response" (going left). Style: flat vector, clean and minimal, educational tone.
-->

### Let's Explore the First Part of the Journey

Now that the cast is introduced, let us focus on what happens **before the Request even leaves Arjun's device.** This is the Client side of the story.

#### Step 1: Arjun Types in the Address Bar

When Arjun types **www.bbc.co.uk/food/recipes/spaghetti** into Chrome and presses Enter, the first thing that happens is that the Browser reads what was typed.

That string of characters is called a **URL**, which stands for **Uniform Resource Locator.** Think of a URL as a precise address on the Web, the same way a postal address tells a delivery driver exactly which building, which street, and which city to go to. The URL tells the Browser exactly which resource to go and fetch from which location on the Web.

We will break down the anatomy of a URL in detail in the next article. For now, just understand that the URL is the starting instruction. It tells the Browser: "Go and get me this specific thing from this specific place."

#### Step 2: The Browser Parses the URL

Once the URL is submitted, the Browser does not immediately sprint off to the Internet. It first takes a moment to **parse** the URL, which means it reads and understands each part of it.

It identifies: which rules to use to communicate (the scheme), which Server to contact (the domain), and which specific page or File to request on that Server (the path).

Again, the detailed breakdown of what each part means comes in the next article. For now, the key thing to understand is that the Browser is doing some reading comprehension before doing anything else. It needs to understand the instruction before it can act on it.

<!-- 
IMAGE: A URL string "www.bbc.co.uk/food/recipes/spaghetti" displayed prominently. Colored brackets highlight different segments of the URL, each with a label below: "Protocol / Scheme", "Domain", "Path". The Browser window is shown above, like the URL is being examined under a magnifying glass. Style: clean annotated diagram, dark background with color-coded highlights.
-->

#### Step 3: Checking the Cache First

Here is something that most people do not know: before the Browser goes out to the Internet to fetch anything, it checks a local storage area called the **Cache** (pronounced "cash").

The Cache is like a short-term memory for the Browser. Every time the Browser successfully loads a Website, it saves certain Files from that Website on your Computer. Images, stylesheets, scripts, and other Files that do not change very often get saved locally.

So when Arjun types in the BBC recipe URL, the Browser first asks itself: "Have I been to this page recently? Do I already have a saved copy of some of these Files?" If it does, it can serve those Files directly from the Cache instead of fetching them again from the Server. This makes the page load significantly faster.

Think of it like this. Imagine every time you wanted to read a book, you had to go to the library, wait in line, take the book out, read it, and return it. But what if you had already borrowed that book last week and it was still sitting on your desk at home? You would just pick it up from your desk. The Cache is that book on your desk.

If the Cache has a fresh, valid copy of what is needed, the Browser can skip the trip entirely or use a mix of cached and fresh Files.

If it does not, the journey continues.

<!-- 
IMAGE: Two-path illustration. A browser icon on the left with a fork in the road. The left path is labeled "Cache Hit" and shows a short arrow going directly to the rendered page (fast). The right path is labeled "Cache Miss" and shows a long arrow going out through a network to a server and back (slow). Style: clean flat vector, two-tone color for each path.
-->

#### Step 4: The Browser Asks the Operating System for Help

If the Cache does not have what is needed, the Browser hands the next part of the job to the **Operating System**, which is the software managing your entire Computer: Windows, macOS, or Linux.

The Browser knows the name of the Server it needs to reach, in Arjun's case, the BBC's Server. But it does not know the actual Network address of that Server, the numerical address that Computers use to find each other on the Internet. It only has the human-readable name.

Finding that numerical address is a process called **DNS Resolution**, and the Operating System is the one who kicks that process off by talking to the Network.

We will cover DNS in full detail in the next article. For now, understand that the Browser essentially says to the Operating System: "I know the name of the place I need to reach. Can you figure out its actual address so we can get there?"

#### Step 5: Building the Request

Once the Operating System has gathered the address information and handed it back to the Browser, the Browser is ready to send the actual Request.

But the Browser does not just shout the URL into the Internet and hope for the best. It builds a carefully structured message called an **HTTP Request.**

**HTTP** stands for **HyperText Transfer Protocol.** A Protocol is an agreed set of rules that two parties follow to communicate with each other. HTTP is the agreed language that Browsers and Servers use to talk to each other on the Web. When a Browser wants to ask a Server for a page, it writes its request in HTTP.

Think of it like sending a formal letter. You do not just scribble "send me the recipe" on a scrap of paper. A formal letter has a specific structure: your address, the recipient's address, a subject line, a greeting, the body of the letter, and a sign-off. Both the sender and recipient understand this format, which makes the communication efficient and unambiguous.

An HTTP Request has a similarly structured format.

#### How an HTTP Request Is Structured

An HTTP Request is made up of three main parts:

**1. The Request Line**

This is the first and most important line. It contains:

- The **Method**: a word that tells the Server what kind of action is being requested. The most common method is **GET**, which simply means "please give me this resource." When Arjun loads a recipe page, his Browser is making a GET request. Another common method is **POST**, which means "I am sending you some data." When you fill out a form or log in to a Website, your Browser is often making a POST request.

- The **Path**: the specific location of the resource on the Server. For Arjun, this would be **/food/recipes/spaghetti**.

- The **HTTP Version**: which version of the HTTP rules the Browser is using.

**2. The Headers**

Headers are additional pieces of information the Browser sends along with the Request. They are like the metadata of the message. They tell the Server things like: what kind of device is making the request, what language the User prefers, what kind of file formats the Browser can handle, and whether there is any saved information (like a login session) to pass along.

You can think of Headers as the details section of the formal letter: additional context that helps the Server understand exactly what is being asked and by whom.

**3. The Body**

For a GET Request like Arjun's, there is no Body. He is just asking for something, not sending anything. But for a POST Request, the Body is where the actual data being sent lives. For example, when you log into a Website, your username and password travel in the Body of the HTTP Request.

<!-- 
IMAGE: A clean visual breakdown of an HTTP Request formatted like a card or letter. Section 1 labeled "Request Line" shows: GET /food/recipes/spaghetti HTTP/1.1. Section 2 labeled "Headers" shows a list of key-value pairs: Host: www.bbc.co.uk, Accept: text/html, Accept-Language: en-US. Section 3 labeled "Body" shows: (empty for GET requests). Each section is a different color. Style: code-card aesthetic, dark background, monospace font, clean and readable.
-->

#### Step 6: The Request Leaves the Device

The HTTP Request is now fully built. The Browser packages it up and passes it to the Operating System, which passes it to the Network Interface Card on Arjun's Computer, which sends it out through his Wi-Fi Router and onto the Internet.

At this point, Arjun's part of initiating the journey is done. The Request is now travelling.

Where it goes next, how it finds the BBC's Server among billions of Computers on the Internet, and how the Server sends everything back, is the story of the next article.

### Let's Define Things Now...

> **User:** The human being interacting with the Browser and initiating the Web experience.

> **Client:** Any device or software that requests information or a service from another Computer. In Web terms, the Browser is the Client.

> **Server:** A Computer that stores Website Files and responds to incoming Requests by sending those Files back to the Client.

> **Request:** A structured message sent from the Client to the Server, asking for a specific resource.

> **Response:** The structured message sent back from the Server to the Client, containing the requested resource or an explanation of why it could not be provided.

> **URL (Uniform Resource Locator):** A precise address on the Web that points to a specific resource on a specific Server.

> **Cache:** A local storage area where the Browser saves copies of recently loaded Files to speed up future visits to the same Website.

> **HTTP (HyperText Transfer Protocol):** The agreed set of rules that Browsers and Servers follow to communicate on the Web.

> **Protocol:** A set of agreed rules that allow two parties to communicate reliably and consistently.

### Activity: Request Detective, Part 1

Before reading the next article, spend five minutes on this observation exercise. No coding required.

1. Open any Website in Chrome or Firefox.
2. Right-click anywhere on the page and select **Inspect** to open DevTools.
3. Click on the **Network** tab at the top of the DevTools panel.
4. Reload the page using **Ctrl + R** (or **Cmd + R** on Mac) so that the Network tab captures all the Requests made.
5. Look at the list of Requests that appear. Click on the very first one, which is usually the main HTML File.
6. In the panel that opens, find and note down:
   - The **URL** that was requested
   - The **Method** (it should say GET)
   - The **Status Code** (a number, probably 200)

Do not worry about understanding everything you see yet. The goal is simply to observe. You are looking at a real HTTP Request in the wild. Everything you see there connects directly to what this article covered.

## What's Coming Next...

Arjun's Request is now out on the Internet. But the Internet is a network connecting billions of Computers. How does his Request know which Computer to go to? How does the name "www.bbc.co.uk" get translated into a specific numerical address? What happens when his device and the BBC's Server establish a connection? And what does the Server actually send back?

The next article goes under the hood on all of this: URL anatomy, the Domain Name System, and how two Computers shake hands before any Files are exchanged.
