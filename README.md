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


