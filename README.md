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


## Commit 3 Reflection Notes  

Milestone 3 enhances the web server by implementing basic request handling and error responses. Previously, the server always returned `hello.html` regardless of the request. This update introduces logic to check the requested URL and serve different files accordingly.  

The implementation modifies the `handle_connection` function to extract the first line of the HTTP request. A conditional statement checks whether the request is for the root path (`GET / HTTP/1.1`). If so, the server returns `hello.html` with a `200 OK` status. Otherwise, it responds with `404.html` and the status `404 NOT FOUND`.  

Additionally, file reading is now dynamic, as the server selects the appropriate file (`hello.html` or `404.html`) based on the request. This update allows the server to handle missing pages gracefully by serving a predefined error page instead of crashing or returning an invalid response.  

Testing the server by accessing `http://127.0.0.1:7878` correctly loads `hello.html`, while requesting an unknown path (e.g., `http://127.0.0.1:7878/unknown`) returns `404.html`. This milestone establishes basic request handling and prepares the server for more advanced routing in future updates.  

![Commit 3 screen capture](/assets/images/commit3.png)  


## Commit 4 Reflection Notes  

The modified `handle_connection` function introduces a delay for requests to `/sleep` by calling `thread::sleep(Duration::from_secs(10))`. When accessing `127.0.0.1:7878/sleep` in one browser tab and `127.0.0.1:7878` in another, both requests experienced delays. However, when testing `127.0.0.1:7878` alone, the page loaded quickly, confirming that the issue was not due to device lag.  

This occurs because the server processes requests sequentially using a single thread. While handling the `/sleep` request, the server becomes blocked, preventing it from responding to other incoming requests until the delay completes. This highlights a fundamental drawback of single-threaded servers: a slow request can stall all others, leading to poor performance in multi-user environments.  

To mitigate this, a multithreaded approach can be implemented, allowing the server to handle multiple requests concurrently and preventing one slow operation from affecting others. This will be addressed in the next milestone.  


## Commit 5 Reflection Notes  

The `ThreadPool` implementation enables the server to handle multiple requests simultaneously rather than sequentially. It achieves this by maintaining a set number of worker threads that remain idle until tasks are assigned. When a request arrives, it is delegated to an available worker, preventing the main thread from being blocked.  

This approach ensures that slow requests do not delay others, enhancing performance under high traffic. Each worker thread continuously retrieves tasks from the queue, processes them, and then awaits the next task. This design minimizes the overhead of repeatedly creating and destroying threads while keeping the server efficient and responsive.  


## Bonus Commit Reflection Notes  

The `build` function enhances thread pool creation by explicitly returning a `Result`, providing a structured and reliable way to handle errors. Instead of causing a panic when an invalid size (such as zero) is provided, it returns a `PoolCreationError`, allowing the caller to manage the failure gracefully.  

This improves code robustness and predictability, adhering to Rust’s best practices for error handling. By using `Result<ThreadPool, PoolCreationError>` instead of `Option<ThreadPool>` or an immediate `panic!`, the implementation ensures that errors are handled properly, leading to safer and more stable execution. Additionally, this design makes future error-handling extensions more seamless without affecting existing functionality.  