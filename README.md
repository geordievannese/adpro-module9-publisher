1. How much data your publisher program will send to the message broker in one run?

When the publisher runs, it delivers five individual messages to the broker in a single execution. Each message carries two pieces of information user_id (a brief identifier such as "1" through "5") and user_name (an alphanumeric string roughly twenty characters in length). When encoded with Borsh, each record ends up occupying roughly 30–40 bytes, so all five messages together total somewhere between 150 and 200 bytes. Because that payload is so modest, the complete batch is marshaled and transmitted to the broker almost instantaneously.

2. The url of: “amqp://guest:guest@localhost:5672” is the same as in the subscriber program, what does it mean?

This URI tells each client to speak AMQP (Advanced Message Queuing Protocol) and to authenticate with the default “guest” account. The first guest is the username, the second is its password—these are the out-of-the-box credentials RabbitMQ ships with for local development.

By specifying localhost, both programs point to a broker running on the same machine where they’re launched. The port 5672 is RabbitMQ’s standard listener for AMQP traffic. Because both publisher and subscriber share every part of this URI—protocol, credentials, host, and port—they end up connecting to the exact same RabbitMQ instance.

Once connected to that shared broker, any messages the publisher sends to a queue or exchange become immediately available for the subscriber to consume. In other words, using a common connection string guarantees that both ends talk to the same messaging backbone, enabling seamless message handoff.


3. ## Running RabbitMQ as message broker.
![Screenshot 2025-05-14 at 08.53.44.png](img/Screenshot%202025-05-14%20at%2008.53.44.png)


4. ## Sending and processing event
![Screenshot 2025-05-14 at 09.05.47.png](img/Screenshot%202025-05-14%20at%2009.05.47.png)

5. ## Monitoring chart based on publisher.
![Screenshot 2025-05-14 at 08.59.11.png](img/Screenshot%202025-05-14%20at%2008.59.11.png)

When I execute the publisher application, I notice an abrupt surge in the “messages published per second” metric on the RabbitMQ management dashboard. This spike corresponds precisely to the batch of messages I pushed into the broker in that single run. Even though the queue depth report remains at zero, it doesn’t mean messages weren’t delivered—it simply indicates that my consumer was online and pulled them off the queue almost immediately, preventing any backlog.

Alongside the publication rate, I also see a matching rise in the acknowledgement (ACK) curve, often rendered in purple. Those ACKs represent my consumer’s confirmation that it received and processed each message. Seeing the ACK line climb in lockstep with the publication line is a clear sign that the end-to-end flow is functioning: messages are being enqueued, delivered to the consumer, and then explicitly acknowledged.

Together, these two charted lines—publications in one color and acknowledgements in another—provide real-time feedback that my publisher, RabbitMQ broker, and subscriber are all communicating correctly. The broker isn’t merely storing messages; it’s actively forwarding them to consumers, and those consumers are signaling back that processing succeeded.