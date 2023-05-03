Download Link: https://assignmentchef.com/product/solved-cs3516-project-2-implementing-reliable-data-transfer-protocol-2
<br>
<strong>      </strong>         1. Overview

In this programming assignment, you will be writing the sending and receiving transport-level code for implementing a simple reliable data transfer protocol, i.e., <strong>the Alternating-Bit-Protocol (ABP).</strong> This project should be <strong>fun </strong>since your implementation will differ very little from what would be required in a real-world situation.




Since you probably don’t have standalone machines (with an OS that you can modify), your code will have to execute in a simulated hardware/software environment. However, the programming interface provided to your routines, i.e., the code that would call your entities from above and from below is very close to what is done in an actual Linux environment. (Indeed, the software interfaces described in this programming assignment are much more realistic than the infinite loop senders and receivers that many texts describe). Stopping/starting of timers are also simulated, and timer interrupts will cause your timer handling routine to be activated.




Figure 1 below presents an overview of the elements you will be coding in this project. Our purpose of this project we only have three layers in our network stack.










<strong>Figure     1:              Project   Overview                </strong>

The procedures you will write are for the sending entity (A) and the receiving entity (B). The equivalent reverse (bidirectional) traffic originates on the B-side and is received on the A-side. Of course, the B-side will have to send packets to A to acknowledge (positively or negatively) receipt of data. Your routines are to be implemented in the form of the procedures described in Figure 1. These procedures will be called by (and will call) procedures already written that emulate a network environment.

<h1>2. Communication Between Layers – Data Structures</h1>




Figure 2 below shows the data structure used for communicating between the layers







<strong>Figure     2:               Data         Structure                 for            Communicating    between Layers </strong>

<h1>3. Routines you will write</h1>

The routines you will write are detailed below. As noted above, such procedures in real-life would be part of the operating system, and would be called by other procedures in the operating system.




<h1>void A_output(struct msg message);</h1>

Where message is a structure of type <strong>msg</strong>, containing data to be sent to the B-side. This routine will be called whenever the upper layer at the sending side (A) has a message to send. It is the job of your protocol to insure that the data in such a message is delivered in-order, and correctly, to the receiving side upper layer.




<h1>void A_input(struct pkt packet);</h1>

Where packet is a structure of type <strong>pkt</strong>. This routine will be called whenever a packet sent from the B-side (i.e., as a result of a <strong>tolayer3()</strong> being done by a B-side procedure) arrives at the Aside. The arriving packet may be corrupted w.r.t. packet sent from the B-side.

<strong> </strong>

<h1>void A_timerinterrupt();</h1>

This routine will be called when A’s timer expires (thus generating a timer interrupt). You’ll probably want to use this routine to control the retransmission of packets. See <strong>starttimer()</strong> and <strong>stoptimer()</strong> below for how the timer is started and stopped.




<h1>void A_init();</h1>

This routine will be called once, before any of your other A-side routines are called. It can be used to do any required initialization.




<h1>void B_input(struct pkt packet);</h1>

Where packet is a structure of type <strong>pkt</strong>. This routine will be called whenever a packet sent from the A-side (i.e., as a result of a <strong>tolayer3()</strong> being done by a A-side procedure) arrives at the Bside. packet is the (possibly corrupted) packet sent from the A-side.

<h1>void B_init();</h1>

This routine will be called once, before any of your other B-side routines are called. It can be used to do any required initialization.




<h1>void B_timerinterrupt();</h1>

This routine will be called when B’s timer expires (thus generating a timer interrupt). You’ll probably want to use this routine to control the retransmission of packets. See <strong>starttimer()</strong> and <strong>stoptimer()</strong> below for how the timer is started and stopped.




<h1>void B_output(struct msg message);</h1>

Similar to <strong>A_Output()</strong> Required only when implement bi-directional messaging. <strong>Ignore for this project. </strong>

<strong> </strong>

<h2>4. Available routines, to be used by your routines</h2>

The procedures described above are the ones that you will write. The following routines, which can be called by your routines:

<strong> </strong>

<h1>void startTimer(int AorB, double TimeIncrement);</h1>

Where <strong>calling_entity</strong> is either <strong>AEntity</strong> (for starting the A-side timer) or <strong>BEntity </strong>(for starting the B side timer), and T<strong>imeIncrement </strong>is a double value indicating the amount of time that will pass before the timer interrupts. A’s timer should only be started (or stopped) by A-side routines, and similarly for the B-side timer. To give you an idea of the appropriate increment value to use: a packet sent into the network takes an average of 5 time units to arrive at the other side when there are no other messages in the medium.




<h1>void stopTimer( int AorB );</h1>

Where <strong>calling_entity</strong> is either <strong>AEntity</strong> (for stopping the A-side timer) or <strong>BEntity</strong> (for stopping the B side timer).




<h1>double getClockTime();</h1>

Returns the current simulation time, which is very useful in printouts of traces since the simulator is already printing out such times.




<strong>int getTimerStatus( int AorB ); </strong>

Returns <strong>TRUE</strong> if the requested timer is running, or <strong>FALSE</strong> if the timer is not running.




<h1>void toLayer3( int AorB, struct pkt packet );</h1>

Where <strong>calling</strong>_<strong>entity</strong> is either <strong>AEntity</strong> (for the A-side send) or <strong>BEntity</strong> (for the B side send), and packet is a structure of type <strong>pkt</strong>. Calling this routine will cause the packet to be sent into the network, destined for the other entity.




<h1>void toLayer5( int AorB, struct msg datasent);</h1>

Where <strong>calling</strong>_entity is either <strong>AEntity</strong> (for A-side delivery to layer 5) or BEntity (for B-side delivery to layer 5), and message is a structure of type <strong>msg</strong>. With unidirectional data transfer, you would only be calling this with <strong>calling</strong>_<strong>entity</strong> equal to <strong>BEntity</strong> (delivery to the B-side). Calling this routine will cause data to be passed up to layer 5.




<h2>5. Input Arguments</h2>

The medium is capable of <em>corrupting, losing, and reordering</em> packets. When you compile your procedures and simulation procedures together, and run the resulting program, you will be asked to specify values regarding the simulated network environment. Figure 3 illustrates a sample input and below that you will find a description for the various input elements.




<strong>Figure     3:              Input       Arguments</strong><strong>             </strong>

<strong>Number of messages to simulate: </strong>

My emulator (and your routines) will stop as soon as this number of messages have been passed down from layer 5, regardless of whether or not all of the messages have been correctly delivered. Thus, you need not worry about undelivered or unACK’ed messages still in your sender when the emulator stops. Note that if you set this value to 1, your program will terminate immediately, before the message is delivered to the other side. Thus, this value should always be greater than 1.




<strong>Loss: </strong>

You are asked to specify a packet loss probability. A value of 0.1 would mean that one in ten packets (on average) are lost and not delivered to the destination.




<strong>Corruption: </strong>

You are asked to specify a packet loss probability. A value of 0.2 would mean that two in ten packets (on average) are corrupted. Note that the contents of payload, sequence, ack, or checksum fields can be corrupted. Your checksum should thus include the data, sequence, and ack fields.




<strong>Out Of Order: </strong>

You are asked to specify an out-of-order probability. A value of 0.2 would mean that two in ten packets (on average) are reordered.




<strong>Tracing: </strong>

Setting a tracing value of 1 or 2 will print out useful information about what is going on inside the emulation (e.g., what’s happening to packets and timers). A tracing value of 0 will turn this off. A tracing value of 5 will display all sorts of odd messages that are for emulator-debugging purposes. A tracing value of 2 may be helpful to you in debugging your code. You should keep in mind that real implementers do not have underlying networks that provide such nice information about what is going to happen to their packets! You will certainly find tracing your own code is helpful. When the time comes to show off your code, you must have a way of turning off all your debugging messages. (We will be running with tracing = 1, so you can set your messages to be displayed only for a higher tracing level – like 3 or 4.




<strong>Average time between messages from sender’s layer5: </strong>

You can set this value to any non-zero, positive value. Note that the smaller the value you choose, the faster packets will be arriving to your sender.




<strong>Randomization:  </strong>

The simulation works by using a random number generator to determine if packets will or will not be modified in some fashion. Setting 0 here (no randomization) means that you will get the same result for each of your runs. This can be extremely valuable for debugging. However, for real testing, you must run with randomization = 1 to see what problems you can shake out. When you demonstrate your code, I expect to see randomization enabled.




<strong>Direction:  </strong>

The possibilities are Unidirectional = 0, Bidirectional = 1 (Which should not be needed).







<h2>6. Environment</h2>

There are three files that you will use to implement your solution:

<strong>project2.c</strong> – contains the simulation code for the network layer and for the application layer 5.  <strong>student2.c</strong> – contains the stub of the numerous routines you are to write.  <strong>project2.h</strong> – various definitions and data structures that are included in both of the source code modules.

You can download the project 2 package (including these environment code) from here  <u>https://users.wpi.edu/~yli15/courses/CS3516Fall18A/projects/Proj2/project_2_A18.zip</u>




Note that this simulation runs on both Windows and LINUX. You should modify the location in Project2.h that specifies the OS. However your project will be evaluated on the ccc/rambo machines using Linux.




<h1>Compile the sources using gcc –g project2.c student2.c</h1>







You may want to divide the file student2.c into three components – my description implies you create three source files – but the details are up to you.

<ol>

 <li><strong>c</strong> contains the functions having well known names for the A Entity. The interfaces are here, the state information is here, but the routines that do all the work are kept in the common routine.</li>

 <li><strong>c</strong> contains the functions having well known names for the B Entity. The interfaces are here, the state information is here, but the routines that do all the work are kept in the common routine.</li>

 <li><strong>c</strong> contains all the code common to both A and B. Since A and B are really identical, the methods are here. But don’t keep any state information here.</li>

</ol>







<h2>7. Actual Project</h2>

<strong>Now that you have an idea of the simulation environment and what you need to modify, we can talk about what you need to implement. </strong>

<strong> </strong>

<strong>Alternating bit Protocol  (RDT 3.0) </strong>

You are to write the procedures, A_output(), A_input(), A_timerinterrupt(), A_init(), B_input(), and B_init() which together will implement a stop-and-wait (i.e., the alternating bit protocol, which we referred to as rdt3.0 in the text in Section 3.4.1) unidirectional transfer of data from the A-side to the B-side. Your protocol should use both ACK and NACK messages.




You should choose a very large value for the average time between messages from sender’s layer5, so that your sender is never called while it still has an outstanding, unacknowledged message it is trying to send to the receiver. I’d suggest you choose a value of 1000. You should also perform a check in your sender to make sure that when A_output() is called, there is no message currently in transit. If there is, you will need to buffer the message until the previous transaction is completed.




<h2>8. Helpful Hints</h2>

<strong>Checksumming:</strong> You can use whatever approach for checksumming you want. Remember that the sequence number and ack field can also be corrupted. A simple addition of all the bytes in the packet will NOT work – this is because I have diabolically defined corruption to be the swapping of bytes from two locations in the packet – a simple addition will give the same sum even with the packet corrupted.

Note that any shared “state” among your routines needs to be in the form of global variables. Note also that any information that your procedures need to save from one invocation to the next must also be a global (or static) variable. For example, your routines will need to keep a copy of a packet for possible retransmission. It would probably be a good idea for such a data structure to be a global variable in your code. Note, however, that if one of your global variables is used by your sender side, that variable should NOT be accessed by the receiving side entity, since in real life, communicating entities connected only by a communication channel can not share global variables.

There is a double global variable called CurrentSimTime that you can access from within your code to help you out with your diagnostics messages. It represents the time as understood by the simulation.

<strong>START SIMPLE:</strong> Set the probabilities of loss and corruption to zero and test out your routines. Better yet, design and implement your procedures for the case of no loss and no corruption, and get them working first. Then handle the case of one of these probabilities being non-zero, and then finally both being non-zero.




<strong>Debugging:</strong> We’d recommend that you set the tracing level to 2 and put LOTS of printf’s in your code visible with debug level = 2. The output needs to be clean when we look at it. We will be running with debug_level = 1.




<strong>Random Numbers.</strong> The emulator generates packet loss and errors using a random number generator. Our past experience is that random number generators can vary widely from one machine to another. You may need to modify the random number generation code in the emulator we have supplied you. Our emulation routines have a test to see if the random number generator on your machine will work with our code. If you get an error message:

<ul>

 <li>It is likely that random number generation on your machine
” );</li>

 <li>Is different from what this emulator expects. Please take
”);</li>

 <li>A look at the routine GetRandomNumber() in the emulator code. Sorry. Then you will need to sort out the routine.</li>

</ul>




<h2>9. Evaluation Criteria</h2>

<strong>Alternating Bit Protocol:  </strong>

<ul>

 <li>Is there a clean output, free from messy debugging messages?</li>

 <li>Does the project work with corruption on?</li>

 <li>Does the project work with lost packets?</li>

 <li>Does the project work with packet out-of-order?</li>

 <li>Does the project work with randomization?</li>

</ul>

Others:

<ul>

 <li>Is the code commented? Is it free of numerical constants sprinkled in the code? Is it indented correctly?</li>

 <li>Is there a REAL makefile both phases of the project?</li>

 <li>Is there is a sufficient README file for both phases of the project?</li>

</ul>







<h2>10. Submission</h2>

As a part of the submission you need to provide the following:




For Alternating-bit-Protocol (ABP), create a zip file <strong>“your-wpi-login_project2_ABP.zip”</strong>

<ol>

 <li>All the code for the alternating-bit protocol (any .c/.c++ and .h files), including any code that was pre-provided as part of this project</li>

 <li>A ReadMe (txt) file that spells out exactly how to compile and run the code</li>

 <li>A PDF document with the output trace as described in the project description</li>

</ol>




Submit your document electronically via Canvas (https://canvas.wpi.edu) by 11:59pm on the day the assignment is due. Make sure you choose “Project 2” under project drop-down in <u>Canvas</u> before uploading the zip file.




<h2>11. Grading</h2>

Grade breakup (%) assuming all documents are present:

<ol>

 <li>Functioning Alternating Bit-Protocol code = 75%</li>

 <li>Alternating Bit-Protocol output trace (as explained in the project description) = 15%</li>

 <li>README = 5%</li>

 <li>Makefile = 5%</li>

</ol>




The grade will be a ZERO if:

<ol>

 <li>If the code does not compile or gives a run-time error</li>

 <li>If README with detailed instructions on compiling the running the code is not present</li>

</ol>


