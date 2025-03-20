## Commit 1 Reflection Notes  

Milestone 1 focuses on implementing a basic single-threaded web server in Rust. The process begins with setting up a new Rust project using `cargo new hello`, followed by initializing a Git repository and pushing the initial commit.  

The first implementation establishes a TCP listener using `TcpListener::bind("127.0.0.1:7878")`, which continuously listens for incoming connections. Whenever a client connects, the server prints `"Connection established!"` to the terminal. At this stage, the server does not process HTTP requests but only acknowledges connections.  

To enhance functionality, a `handle_connection` function is introduced. This function reads incoming HTTP requests using `BufReader` and stores them in a vector. Running the modified server allows the inspection of raw HTTP request headers when accessing `http://127.0.0.1:7878` from a browser, demonstrating how web requests are structured and processed at a low level.  

A key observation from this milestone is that a single-threaded server processes requests sequentially, meaning it can only handle one connection at a time. While functional, this approach has scalability limitations, which will be addressed in later milestones. This milestone establishes a foundational understanding of Rust’s TCP networking capabilities and HTTP request handling.  


## Commit 2 Reflection Notes  

Milestone 2 extends the functionality of the web server by enabling it to send an actual HTML response to the client. Previously, the server only acknowledged connections and displayed HTTP request headers in the terminal. In this milestone, the server is modified to read an HTML file and send it as a response to incoming requests.  

The implementation introduces the `fs::read_to_string` function to load an HTML file (`hello.html`) from disk. The server constructs an HTTP response by including a status line (`HTTP/1.1 200 OK`), a `Content-Length` header, and the contents of the HTML file. The response is then sent to the client using `stream.write_all(response.as_bytes())`.  

Testing the server by visiting `http://127.0.0.1:7878` now displays the contents of `hello.html` in the browser, confirming that the server can handle basic HTTP responses. This milestone demonstrates how a web server processes requests and serves static files, forming the basis for more advanced request handling in later stages.  

![Commit 2 screen capture](/assets/images/commit2.png)