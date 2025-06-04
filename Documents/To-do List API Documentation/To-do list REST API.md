# To-do List API reference

To-do list API follows the [REST](https://en.wikipedia.org/wiki/REST) principles and leverages the [OpenAPI](https://swagger.io/specification/) specification standard to bring an easy tool for fetching task data from a To-do List database.
Below is a brief explanation of its core functionality.

**Note:** This documentation is aimed to provide a concise explanation of concepts required to use To-do List API, that can be understood regardless of one's backgroundand technical expertise. \
Each of the sections on this page builds on top of the information presented in the previous one.

- If you're **entirely unfamiliar with the concept of APIs** we strongly suggest you to first look at the [Basic concepts](#basic-concepts) section which offers a brief explanation of key concepts needed for understanding the rest of this page.

- If you have a **basic understanding** of APIs you can go straight to the [Getting Started](#getting-started) section, which explains how the requests are sent and lists possible parameters and export options.

- If you're **already familiar with Open API/Swagger specification** you can directly go to the [To-do APP OAS documentation](./todo-app-api.yaml) YAML file, which provides all the necessary endpoint documentation, including available request methods, parameter values and response schema.

### Basic concepts
#### What's an API?
> **API** stands for **Application Programming Interface**. In plain terms, API is a set of rules and methods which allow communication between computer programs.
>
> One way to think about APIs is to imagine you're at a restaurant. It would be incredibly chaotic and dangerous if every client walking into the restaurant had a direct access to the kitchen.
> 
> In this scenario API is like a waiter that takes your orders (**requests**) to the kitchen (**the server**) and brings back the food (**response**) once it's ready.

> In short, APIs allow different apps and systems to **exchange information** with each other **without exposing their internal details**. 
##### Protocol
> [Protocol](https://en.wikipedia.org/wiki/Communication_protocol) dictates how the communication between clients and APIs is formatted.
> For our analogy Protocol would simply be the language that you and the waiter speak.
> When you interact with APIs over the web (which is the case for the To-do list API), you're using [HTTP](https://en.wikipedia.org/wiki/HTTP).
##### Endpoints
> An endpoint is a specific **address that an API uses to send or receive information**. For Web APIs endpoints are usually [**URL addresses**](https://en.wikipedia.org/wiki/URL).

> In the context of our restaurant analogy one endpoint would be the table the waiter sits you at when you enter the restaurant and the other would be the chef  which takes the orders forwarded by the waiter.

##### API Specification
> Specification is what standardizes the requests and responses, defining **what data can be requested** and how the response will be **formated and delivered**.
> 
> In the context of our restaurant analogy, API Specification would be the menu that clients can use to make their requests.

#### REST API
> **REST** stands for **Representational State Transfer**. It’s a style of API architecture that works by implementing simple, standard protocols (usually HTTP), making it easy for applications to send requests and receive responses in a consistent way.
> 
> Some of the key features of REST APIs are:
> 1. **Stateless**: Each request from a user must contain all the information needed to process it. The server doesn't store anything about the previous request, which makes it simple and scalable.
> 2. **Cacheable**: Copies of frequently accessed data can be stored (**cached**) in several points acrross the request-response path, which allow for faster operations and reduce the load on the servers.
> 2. **Uses HTTP Methods**: It relies on four standard HTTP methods to interact with the **resources** (data):
>> 	1. `GET` (to get information)
>> 	2. `POST` (to send new information)
>> 	3. `PUT` (to update information)
>> 	4. `DELETE` (to remove information).
> 3. **Data is usually in JSON format**: The data exchanged is often in a format called JSON, which is easy for humans to read and machines to parse.
>
> To learn more about the REST API architecture, you can check out this article: [What is REST?](https://restfulapi.net/)

### Getting started

To-do list uses [curl](https://curl.se/) (Client for URL) for requesting data from the <code>/todos/export</code> endpoint. `-X` argument is used to specify the method (`GET`), whilst `-H` is used to specify the HTTP header in the format `Key: Value` - in this example it tells the server that client experiences the data to be sent in a [CSV](https://www.ietf.org/rfc/rfc4180.txt) format

```bash
curl -X 'GET' \
  'https://simpletodoapp.com/v1/todos/export?includeHeader=true&statusFilter=all&sortBy=id&sortOrder=asc&tagsFilter=urgent&tagsFilter=shopping' \
  -H 'accept: text/csv'
```

Notice how the URL above includes request parameters with values for the following fields: <code>includeHeader</code>, <code>statusFilter</code>, <code>sortBy</code> and <code>tagsFilter</code>.
We will touch briefly on each of those options in the following sections.

#### Formatting your data
<code>includeHeader</code> is a simple *boolean* (true/false) value specifying whether to include the CSV header. CSV header is the first line of the CSV file, which lists all of the column names - it is optional and you might want to omit it depending on your scenario (f. ex. if you want to append records from the API response to an existing table)

<code>sortOrder</code> defines whether the list should be sorted in an ascending (<code>asc</code>) or descending (<code>desc</code>) fashion and <code>sortBy</code> defines which column it will be sorted by (**ID**, **task**, **completion date**, **due date**, **creation date** or **tags**)
#### Filtering data
<code>statusFilter</code> lets you filter results by tasks which have been marked as **completed** or tasks which are still **due**. If not present the request will return both types of tasks (**all**) by default.

<code>tagsFilter</code> filters tasks by a specific tag's presence. In above example we have two tag filters.
In this scenario **records containing at least one of the specified tags will be returned**.
#### Exporting your list
Here's how a sample output looks like:

```
id,task,completed,dueDate,createdAt,updatedAt,tags
1,Buy groceries,false,2024-12-01,2024-11-20T10:00:00Z,2024-11-21T15:45:00Z,"shopping,urgent"
2,Clean the house,true,2024-12-05,2024-11-18T14:30:00Z,2024-11-19T10:10:00Z,"chores"
3,Submit project,false,2024-11-30,2024-11-22T08:00:00Z,2024-11-22T08:30:00Z,"work,priority"
```

As you can see, it is a simple CSV format - values are separated by commas and first line is a **header** definining column names.
After converting the output to a table we get:

| id  | task            | completed | dueDate    | createdAt            | updatedAt            | tags            |
| --- | --------------- | --------- | ---------- | -------------------- | -------------------- | --------------- |
| 1   | Buy groceries   | false     | 2024-12-01 | 2024-11-20T10:00:00Z | 2024-11-21T15:45:00Z | shopping,urgent |
| 2   | Clean the house | true      | 2024-12-05 | 2024-11-18T14:30:00Z | 2024-11-19T10:10:00Z | chores          |
| 3   | Submit project  | false     | 2024-11-30 | 2024-11-22T08:00:00Z | 2024-11-22T08:30:00Z | work,priority   |

#### Responses

HTTP offers [status codes](https://www.iana.org/assignments/http-status-codes/http-status-codes.xhtml) in response to client's request to indicate how the sent request was processed.
Below are the possible responses you can get when sending a To-do list `GET` request:

| HTTP Code |Summary                                                                                       |
| ----------------- | --------------------------------------------------------------------------------------------------------- |
| `200 (SUCCESS)`   | You successfully queried the data and received a CSV output with set parameters in response.              |
| `400 (INVALID)`   | The request parameters are invalid.                                                                       |
| `404 (NOT FOUND)` | This response typically means that whilst the parameters are valid, there is no match for the set values. |

### Open API Specification

As mentioned in the introduction, To-do List leverages the Open API specification (OAS) standard, which provides a clear and structured documentation of To-do's API endpoints, operations, and data formats. The current version of OAS is 3.0.3 and the definition document is in YAML format, which makes it easily readable both for humans as well as computers.

You can use the [Swagger UI](https://editor.swagger.io/) tool to open the following file: [To-do APP OAS documentation (YAML)](./todo-app-api.yaml) and test how the To-do List API requests work.
#### Sending test request
1. On the right side of your screen by expanding the **Export Options** section and pressing the **Try it out** button, you can then set the parameters of your test request
![OAS Export Options](./images/OAS1.png)

2. Once you select all the options and press the **Execute** button, you will see the **Curl request** along with the request **endpoint URL** and the **server response**:
![OAS Execute](./images/OAS2.png)

#### Schema
Once you scroll to the bottom of the document, you will find the **Schema** section.
By expanding the parameters listed on the left you can find the *data type* and a short description of the parameter along with an example of a value that it can have.
![OAS Schema](./images/OAS3.png)
