## Milestone 1

For this section, in order to fully understand the `handle_connection` function, I found it best to explain it line by line. 

1. `fn handle_connection(mut stream: TcpStream)` - This line essentially defines the function that was named 'handle_connection' and takes 'TcpStream' as the input parameter. The use of 'mut' makes it mutable and could then be modified within the function.

2. `let buf_reader = BufReader::new(&mut stream);` - In this line, a buffered reader is created with a mutable reference to the the TcpStream. The buffered reader basically helps with I/O performance.

3. `let http_request: Vec<_> = buf_reader` - This line creates a new variable named 'http_request' and the use of 'Vec<_>' makes it so that the variable would be a vector.

4. `.lines()` - This method creates an iterator for the text in the buffered reader.

5. `.map(|result| result.unwrap())` - This method would then help in tranforming all items in the iterator, applying it to each 'Result'.

6. `.take_while(|line| !line.is_empty())` - This method essentially creates a new iterator that keeps on running until the condition is fulfilled. In this case, once it detects an empty line.

7. `collect();` - This method gathers the results from the previous iterators.

8. `println!("Request: {:#?}", http_request);` - This line simply prints the collected HTTP request lines.

Overall, the `handle_connection` function uses a buffered reader to read lines received from a TCP stream and prints out the resulted HTTP request lines when an empty line is finally encountered.

## Milestone 2: Returning HTML

<img width="1136" alt="commit2" src="https://github.com/PascalPahlevi/advprog-module6/assets/143638456/eb498c8c-7435-4b1d-aaf9-feeb6967cc0a">

## Milestone 3: Validating request and selectively responding

<img width="1021" alt="image" src="https://github.com/PascalPahlevi/advprog-module6/assets/143638456/51b738c7-641c-41e9-a9b2-1921f0b06e6c">

```
let (status_line, filename) = if request_line == "GET / HTTP/1.1" {
        ("HTTP/1.1 200 OK", "hello.html")
    } else {
        ("HTTP/1.1 404 NOT FOUND", "404.html")
    };`

    let contents = fs::read_to_string(filename).unwrap();
    let length = contents.len();

    let response =
        format!("{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}");

    stream.write_all(response.as_bytes()).unwrap();
```

The code above was used for this milestone where it essentially processes the initial HTTP request then determines the most suitable response according to its request by reading the contents of the associated file, ending it by formatting the HTTP response and writing it.

## Milestone 4: Simulation of slow request
```
let (status_line, filename) = match &request_line[..] {
        "GET / HTTP/1.1" => ("HTTP/1.1 200 OK", "hello.html"),
        "GET /sleep HTTP/1.1" => {
            thread::sleep(Duration::from_secs(5));
            ("HTTP/1.1 200 OK", "hello.html")
        }
        _ => ("HTTP/1.1 404 NOT FOUND", "404.html"),
    };
```
This newly added lines of code essentially gives us an idea on how it would be like when multiple people access the website on a single thread server. When the website is given a `/sleep` request, the browser gives out a sleep response of 5 seconds, simulating a slow load time.

## Milestone 5: Multithreaded Server

In order to explain how the multithreading works, there a few blocks of code that would help in doing so.

```
use std::{
    sync::{mpsc, Arc, Mutex},
    thread,
};
```
The block of code above imports a few things to help in setting up the multithreading. First, we have the 'mpsc' import which helps in message passing between theads. Afterwards we have the 'Arc' import which would be used for shared ownership betwen the threads. There is also the 'mutex' import for help in managing the mutable data. Finally, the 'thread' import is used to allow the ability to create threads itself.

```
pub struct ThreadPool {
    workers: Vec<Worker>,
    sender: mpsc::Sender<Job>,
}
```
This block of code creates the struct 'Threadpool' which contains the vector of 'worker' with the use of a sender to send jobs to the workers/

```
impl ThreadPool {
    pub fn new(size: usize) -> ThreadPool {
        assert!(size > 0);

        let (sender, receiver) = mpsc::channel();
        let receiver = Arc::new(Mutex::new(receiver));

        let mut workers = Vec::with_capacity(size);
        for id in 0..size {
            workers.push(Worker::new(id, Arc::clone(&receiver)));
        }

        ThreadPool { workers, sender }
    }
}
```
This block of code creates a new threadpool, with the size parameter indicating the needed amount of workers to be created. The 'mpsc::channel' is used to ensure the existence of a connection between the sender and the receiver, in this case between the main and worker thread.

```
pub fn execute<F>(&self, f: F)
where
    F: FnOnce() + Send + 'static,
{
    let job = Box::new(f);
    self.sender.send(job).unwrap();
}
```
This block of code works as a method in sending jobs to the threadpool where it will be executed. It will take a closure 'F' as an argument and sends it to the sender channel.

```
struct Worker {
    id: usize,
    thread: thread::JoinHandle<()>,
}

impl Worker {
    fn new(id: usize, receiver: Arc<Mutex<mpsc::Receiver<Job>>>) -> Worker {
        let thread = thread::spawn(move || loop {
            let job = receiver.lock().unwrap().recv().unwrap();
            println!("Worker {id} got a job; executing.");
            job();
        });
        Worker { id, thread }
    }
}
```
In this block of code, the struct 'Worker' basically defines an individual worker in the threadpool. In the worker implementation, a new worker thread is created where it takes in an ID and receiver to retriver jobs from the sender channel. Aftewards, it continuously creates a new thread to execute and retrieve jobs.

 



