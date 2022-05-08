# Public-Key-Distribution-Authority
Man-in-the-middle attack proof Public key distribution 
# Nonce Generation
We used uuid from python libraries (Universally unique Identifier), which has many functions.
We used uuid5, which makes an uuid using the sha-1 hash of namespace + any string. We
chose a random string as input for uuid5 and we generated it using secret.choice. The random
string will consist of uppercase alphabets and digits. We used uuid because it is closest we can
get to pure randomness in python. We also used secrets as its a library designed for
incorporating security into our system.
In the end we convert the resulting hex value to an integer.
# Time Generation
We used a datetime object to get current time, our time will have
day/month/year,hour:min:seconds making it even harder to masquerade .
# Encryption/ Decryption
We used RSA to encrypt and decrypt our messages. We generated a 4096bit private public key
pair for our PKDA, and we generated a 1024 private-public key pair for each of our clients. We
chose 4096bit for PKDA because for RSA to work, the message has to be less than key size
and the message from PKDA to the client is the biggest message.
We stuck with 1024 bit for clients as there will be a lot of clients and the messages they send
are all very small. They either send Request+time or ID+Nonce or Nonce1+Nonce2 , therefore
in all cases the messages they send are small.
We have the keypairs stored in the same directory, and we import the keys to python using
RSA.importkey. RSA is available from Crypto.public. We convert the message bytes to int and
we use python’s inbuilt pow function which takes an optional 3rd argument.pow(x,y,[, z])
Python Docs say that it calculates x^y mod z much faster than pow(x,y)%z. Hence we used the
inbuilt fast modular exponentiation.
Therefore for encryption we calculate M^d mod n (for authentication) and M^e mod n (for
confidentiality)
# Assumptions/Setting up Connections
Our program will work for any number of clients. So we had to use multi threading and had to
put some restriction on the order in which the clients will accept connections. Assume there are
3 clients, at the start every client will be listening to every other client as they will know each
other’s port. Now we will manually accept connections for client1, so client1-client2 connection
will be established and client1-client3 connection will be established. Similarly we do the above
process for client2 and client3. Once all connections are established, we have to proceed as
prompted by the terminal and press ‘c’ . All clients must do this before proceeding.
Due to this we will have redundant connections but it's necessary for program correctness in a
multi threaded environment.
Steps to run
1. Run client.py in 2 different terminals a and b
2. In a enter name as client1 and in b enter name as client2
3. Run pkda.py in a new terminal say c.
4. In a press button ‘a’ , in b press button ‘a’ to establish connection to pkda
5. For client1, press option ‘b’ to start accepting connections
6. For client2, press option ‘a’ to start listening to client1, after this press option ‘b’ to start
accepting connections
7. For client1, press option ‘a’ to start listening to client2
8. Now all the connections are established, so press option ‘c’ in client1 and in client2's
respective terminals.
9. For client1, choose option as client2 to start communicating with client2
10. Now the algorithm proceeds as explained below.
11. After the algorithm executes client1 can send messages to client2.
12. Type “hi” in client1’s terminal and the message will be received and seen in client2’s
terminal
13. Now client2 can reply to client1’s message, but if you see the output saying ‘In main
thread’, then you have to the reply again to send to client1.
14. If any of the clients in their respective turns type ‘good bye’ the connection will be
severed.
15. Now the initiator in this case client1, will have to wait 100 seconds to initiate a new
conversation.
# Algorithm
When a particular client accepts a connection from another client it will create a new thread to
serve that particular client. This newly created thread will then ask PKDA for the other client’s
public key along with current time (details explained earlier). The PKDA will return the Time sent
by client1 along with public key of client2 and client1 will check the t1 sent by PKDA and its
stored t1 and verify that they are equal to prevent replay attacks.These steps will be executed
by the ask key function .The thread will then forward the info encrypted by the other client’s
public key to the other client (say client 2 ) along with newly generated nonce (say n1). Now
client2 will also enter the same ask_key function and forward message encrypted using client
1’s public key. Note he will send a newly generated nonce along with the nonce he previously
received. Now client1 would have stored n1 and t1 and will cross verify if the n1 sent by client2
is == n1 stored by client1 .He will now send n2 to client2 and client2 will verify if the n2 stored by
client2 is equal to n2 sent by client1.
