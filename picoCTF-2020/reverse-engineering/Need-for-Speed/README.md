<h1>Need For Speed</h1>
Points: 400

<h1>Category</h1>
Reverse Engineering

<h1>Problem</h1>
<h3>The name of the game is speed. Are you quick enough to solve this problem and keep it above 50 mph? need-for-speed.</h3>

<h1>Solution</h1>

I will show you two ways to solve this task. In the first solution, we will patch the binary to bypass anti-debug (more on that later) and in the second case we will analyze it statically.<br/>

<h2>First way</h2>

While analyzing the settimer function caught my attention, that exit our program with 1 second delay <br/>

![Alt text](https://i.imgur.com/OyuCzCi.png?raw=true "Title")

Basically we can't run our program, because of this method. Let's delete it.<br/>


Original main function:

![Alt text](https://i.imgur.com/mN4I9vE.png?raw=true "Title")


Patched main function:

![Alt text](https://i.imgur.com/nej6pF5.png?raw=true "Title")

Execute patched program:

![Alt text](https://i.imgur.com/ntm11is.png?raw=true "Title")

Flag: <b>PICOCTF{Good job keeping bus #190ca38b speeding along!}</b>


