## Introduction

The last article was a lot to take in, and that is entirely the point.

A simple, everyday action like typing a URL and pressing Enter sets off an entire chain of events: the Browser reads and parses the URL, checks the Cache, asks the Operating System for help, builds a structured HTTP Request, and sends it out onto the Network.

All of that happens before your Request has even left your device.

We covered all of those steps on a broader level in the last article, building a mental picture of the journey. Now it is time to look at the same concepts from a technical point of view and understand the actual mechanics behind each one.

This article will not go deep into every detail, because the goal here is to establish a solid foundation for Web Development, not to become a Network Engineer. But you will walk away knowing exactly what a URL is made of, how a Domain Name gets turned into a real address the Internet can use, and how two Computers agree to start talking to each other before a single File is exchanged.

<!-- 
IMAGE: A split illustration showing the same road from the last article's highway analogy, but this time with labels and measurements overlaid, like a technical blueprint version of the same scene. The friendly highway becomes a detailed engineering diagram. Caption: "Same journey, now under the hood." Style: half illustrated, half blueprint/schematic aesthetic, clean and modern.
-->

### The URL: More Than Just an Address

In the last article, we said a URL is the precise address of a resource on the Web, the same way a postal address points to a specific building on a specific street in a specific city.

Now let us take that postal address and label every part of it.

Look at this URL:

```
https://docs.bbc.co.uk/food/recipes/spaghetti?servings=2#ingredients
```

It looks like a single string of text, but it is actually made up of five distinct, meaningful parts.

<!-- 
IMAGE: The full URL "https://docs.bbc.co.uk/food/recipes/spaghetti?servings=2#ingredients" displayed in large text on a dark background. Each segment is highlighted in a different color with a labeled bracket below pointing to its name: "Scheme (https)", "Subdomain (docs)", "Domain (bbc.co.uk)", "Path (/food/recipes/spaghetti)", "Query String (?servings=2)", "Fragment (#ingredients)". Style: clean annotated code-card, monospace font, color-coded brackets.
-->

#### The Scheme

The very first part, **https://**, is the **Scheme.** It tells the Browser which rules or Protocol to use when making the Request.

**HTTP** (HyperText Transfer Protocol) is the standard language Browsers and Servers use to communicate. **HTTPS** is the secure version of the same thing, where all data traveling between the Browser and the Server is encrypted so that no one in between can read it.

Whenever you see the padlock icon in your Browser's address bar, that means the connection is using HTTPS. On any serious Website today, HTTPS is the default.

#### The Domain

After the scheme comes the **Domain.** In the example above, the domain portion is **docs.bbc.co.uk**.

The Domain is the human-readable name of the Server you want to reach. It is what you actually type when you want to visit a Website. But notice that it has parts within it:

- **docs** is the **Subdomain.** Subdomains are used by websites to organise different sections or services. **docs.bbc.co.uk** might be a documentation section, while **news.bbc.co.uk** might be the news section, all hosted under the same main domain.

- **bbc** is the **Second-Level Domain**, the actual registered name of the Website.

- **.co.uk** is the **Top-Level Domain (TLD).** It indicates the type or country of origin of the Website. **.com** is the most common globally, **.co.uk** is for UK-based companies, **.edu** is for educational institutions, and so on.

#### The Path

After the domain comes the **Path:** **/food/recipes/spaghetti**.

The Path tells the Server which specific page or File you are asking for. Think of the domain as the name of a building and the path as the directions to a specific room inside that building: go to the food section, then to recipes, then find the spaghetti page.

#### The Query String

After the path, the **?servings=2** part is called the **Query String.**

A Query String allows the Browser to send additional information to the Server as part of the URL. The **?** marks the start of the query string, and everything after it is a key-value pair: **servings** is the key, and **2** is the value. This tells the Server: "give me the spaghetti recipe, but filter it for 2 servings."

Query Strings are very common in search results. When you search for something on Google, the search term you typed is passed to Google's Server as a Query String in the URL.

#### The Fragment

The last part, **#ingredients**, is called the **Fragment.**

A Fragment does not get sent to the Server at all. It is handled entirely by the Browser. It tells the Browser to jump to a specific section on the page after it has loaded. If the recipe page has a section with the ID "ingredients", the Browser will scroll directly to it once the page finishes loading.

### Let's Define Things Now...

> **URL (Uniform Resource Locator):** A complete address that points to a specific resource on the Web. It is made up of a Scheme, Domain, Path, and optionally a Query String and Fragment.

> **Scheme:** The part of a URL that specifies which Protocol to use (HTTP or HTTPS).

> **HTTPS:** A secure version of HTTP where all data between the Browser and Server is encrypted.

> **Domain:** The human-readable name of a Server on the Web (for example, bbc.co.uk).

> **Path:** The specific location of a resource within a Server, written as a series of forward-slash-separated segments.

> **Query String:** Optional key-value pairs added to a URL after a **?**, used to send extra information to the Server.

> **Fragment:** The part of a URL after a **#**, used by the Browser to navigate to a specific section of a page.

### DNS: The Internet's Phonebook

Now that we know what a URL is made of, there is still one big problem.

Computers on the Internet do not communicate using Domain Names like **bbc.co.uk.** They communicate using numerical addresses called **IP Addresses** (Internet Protocol Addresses). An IP Address looks something like **151.101.64.81.** Every device and Server on the Internet has one.

The problem is that humans are terrible at remembering strings of numbers. Nobody types **151.101.64.81** into their Browser. They type **bbc.co.uk.** But the Internet only understands the number.

Someone, or rather something, needs to translate between the two.

That something is the **Domain Name System, or DNS.**

Think of DNS exactly like a phonebook. In the old days, if you wanted to call a business, you did not need to memorise their phone number. You just looked them up by name in the phonebook and the phonebook gave you the number.

DNS works the same way. When Arjun's Browser has the domain name **bbc.co.uk** and needs its IP Address, it sends a query to a DNS Server. The DNS Server looks up the domain name in its records and returns the corresponding IP Address. The Browser then uses that IP Address to contact the right Server.

This lookup happens every time you visit a new Website, and it typically takes just milliseconds.

<!-- 
IMAGE: A two-panel illustration. Left panel: a person holding an old-style phonebook open, looking up a name to find a phone number. Right panel: a Browser sending a query labeled "bbc.co.uk?" to a DNS Server, which sends back a response labeled "151.101.64.81". The two panels are visually connected with an equals sign to show they are the same concept. Style: flat vector, clean and educational.
-->

### Let's Define Things Now...

> **IP Address (Internet Protocol Address):** A unique numerical label assigned to every device connected to a Network. It is how Computers find and communicate with each other on the Internet.

> **DNS (Domain Name System):** A distributed system that translates human-readable Domain Names (like bbc.co.uk) into the IP Addresses that Computers use to communicate.

> **DNS Server:** A Computer that stores a directory of Domain Names and their corresponding IP Addresses, and responds to DNS lookup queries.

### The TCP Handshake: Agreeing to Talk

Now Arjun's Browser has the IP Address of the BBC's Server. It knows where to go. But before it sends the actual HTTP Request, something important happens first.

The two Computers shake hands.

In real life, a handshake is a signal of acknowledgment. Before a meeting begins, two people make eye contact, extend their hands, and shake. It is a quick ritual that says: "I see you. I am ready. Let us begin."

In networking, the **TCP Handshake** (also called the Three-Way Handshake) is the digital version of exactly that. Before any real data is exchanged, the Browser and the Server go through a quick three-step exchange to confirm that both sides are present, ready, and able to communicate reliably.

The three steps, in plain English, are:

1. **SYN** (Synchronise): Arjun's Browser says, "Hello BBC Server, I would like to connect. Are you there?"

2. **SYN-ACK** (Synchronise-Acknowledge): The BBC Server replies, "I am here. I received your message. I am ready. Are you ready?"

3. **ACK** (Acknowledge): Arjun's Browser replies, "Confirmed. I am ready. Let us begin."

Only after this three-step exchange is complete does the Browser send the actual HTTP Request carrying the URL and the GET method.

The entire handshake takes a fraction of a millisecond, but it is essential. It ensures that both sides have an open, reliable connection before any real data starts flowing.

<!-- 
IMAGE: A three-step diagram showing the TCP handshake. On the left is a Browser icon (Arjun's device). On the right is a Server icon (BBC). Step 1: an arrow goes from Browser to Server labeled "SYN - Are you there?". Step 2: an arrow goes from Server to Browser labeled "SYN-ACK - I am here. Are you ready?". Step 3: an arrow goes from Browser to Server labeled "ACK - Ready. Let us begin." The three arrows are stacked vertically in sequence. Style: clean sequential diagram, dark background, color-coded arrows, minimal and clear.
-->

### Let's Define Things Now...

> **TCP (Transmission Control Protocol):** One of the core Protocols of the Internet, responsible for establishing reliable connections between two Computers and ensuring data is delivered completely and in order.

> **TCP Handshake (Three-Way Handshake):** A three-step process (SYN, SYN-ACK, ACK) where a Client and a Server establish a connection before any data is exchanged.

> **SYN, SYN-ACK, ACK:** The three messages exchanged during the TCP Handshake. SYN requests a connection, SYN-ACK acknowledges and confirms readiness, ACK finalises the agreement.

### Activity: URL Dissector

Below are five URLs of increasing complexity. For each one, identify and label every part you can: Scheme, Subdomain, Domain, TLD, Path, Query String, and Fragment. Not every URL will have all parts.

1. `https://www.wikipedia.org/wiki/HTML`

2. `http://shop.example.com/products/shoes?color=red&size=10`

3. `https://www.youtube.com/watch?v=dQw4w9WgXcQ`

4. `https://developer.mozilla.org/en-US/docs/Web/HTML#introduction`

5. `ftp://files.example.org:21/documents/report.pdf`

**Bonus:** Go to [nslookup.io](https://www.nslookup.io) and type in any domain name (for example, google.com). Look at the results and find the IP Address that DNS returns for that domain. That is the actual numerical address your Browser would use to contact that Server.

## What's Coming Next...

We now have a complete picture of the Client side of the journey.

The User types a URL, the Browser parses it, checks the Cache, triggers a DNS lookup to convert the Domain Name into an IP Address, performs the TCP Handshake to open a reliable connection, and finally sends the HTTP Request to the Server.

Now the ball is in the Server's court.

The next article shifts to the other side of this conversation: what happens when a Request lands on a Server sitting somewhere else on the planet, how the Server decides what to send back, and what the Response it sends actually looks like when it arrives at Arjun's Browser.
