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
