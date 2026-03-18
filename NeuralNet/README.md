# **Neural net maker/runner**

Also: **do not try and run .bin or .dylib files found here**, compile them yourself, they may compile differently.
the reason that .bin files are commited as of now (no gitignore file for those) is because neural nets are saved as .bin files (as per now) and I want to keep track of the ones I have already built/trained.

# **Documentation:**

This is a system for generating, training and calculating smaller neural networks, coded in C.
currently, the system is devided into three main parts:

### Generation part:
The generation part, is a piece of C code made to just generate a .bin file where your neural network is stored, and where other parts of the program get the most rudemenary info needed about the neural net. This is usualy only used once at the beginning of the process of generating a nerual net. More info about the Generation code can* be found bellow.

### Traning part
The second part of the system is the code that is for traning the neural net, it is the code where incremental changing of the neural net is done, and is also coded in C. It is made in the random evoluiton filosofy of Neural net traning, because it is the easiest for the user to code for. 
**This is not a self contained piece code**, and also has to be compiled directly for a spesific nerual net. this is done to keep memory use as small as possible, because the nerual net and relevant data is almost entierly stored in static memory, to not waste cost on garbage collection or similar things, for small nerual networks this doesnt matter as much, but for medium to larger nets, this is probably good to have. The traning code does not feature its own loop, and does not test or grade a system, currently. the traning part is a dylib type C file, opperating as a liberary of functions that are currently called by python code. Python takes care of testing a net, by setting up the "game-world" but C does all the heavy lifting, and there are spesific ways to call functions from python to cause different things to happen, which is detailed bellow.

***EXCLAIMER***
The traning code is not done, currently, no changes are saved to the in memory neural net, or the neural net file, and the amount of random evolution we do is constant, meaning that we dont get closer to a better system in an efficient way. 
In the future the loop running, Static value setting and building, and incrementing of random noise strength will be controlled by the controller, which will be programmed in something other than python.

### Running part
This is the part of the system that is used when you are finished and actually using the neural net, this code aswell needs to be altered and compiled for your spesific neural net for memory reasons, in the same way as with the traning part. This code is also used as a dylib type c file, where your code calls a C function with your nerual net input data, where you get the output data in return.

More info can be found below.



---
## **Neural net basic structure**

the neural nets this program is designed for, is the very standard type. they can be broken into three parts, the input layer, hidden layers, and output layers.

notes:  input layer does not do sigmoid or has a bias, output layer has bias and sigmoid, does not have weights* (fact-check about sigmod and bias for output)



