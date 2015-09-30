---
name: Limbajul de Programare Serpent
category: 
---

###Operatiile limbajului de programare Serpent

Aritmetica

•	Aritmetica normala functioneaza dupa cum ne asteptam:: x = 20 + 7 * 5 fixeaza  x ca 55
•	Numerele negative nu exista:  x = 0 - 1 seteaza x la  115792089237316195423570985008687907853269984665640564039457584007913129639935
•	Decimalele nu exista: x = 7 / 4 seteaza x la 1
•	Se poate folosi #/ si  #% pentru a se putea pretinde ca numerele sunt numere intregi: x = (0-9) #/ 3 + 5 seteaza x la 2, pe cand y = (0-9) / 3 + 5seteaza y la 38597363079105398474523661669562635951089994888546854679819194669304376546647
•	Aritmetica deviaza  2^256: x = 3 ^ (2 ^ 254) setand x la 1 (aceasta este o consecinta a Teoremei Euler)
•	Folositi  x = y | z, x = y & z si  x = y xor z pentru operatii bitwise 
Variable
•	Variablele functioneaza ca orice fel de limbaj : x = 25 atunci y = x + 5 seteaza y la 30
•	Folositi x = array(n) pentru a initializa o multime de valori n 32-byte 
•	Folositi x[i] = v pentru a seta valori in multimi si  v = x[i] pentru a obtine valori in multimi
•	Folositi x = bytes(n) pentru a initializa un sir/multime de bytes de n bytes
•	Folositi setch(x,i,v) pentru a seta to set and v = getch(x,i) to get
•	Use array literals to declare arrays inline: x = [1,2,3]
Variables are encoded into EVM code by assigning a memory index to each variable, with 32 bytes of spacing between variables. In the last example, x literally is the memory index of the start of the array, which is itself stored in a dynamically allocated slice of memory. Note that bounds-checking is not performed; array(3)[5] = 10 will have completely unknown and likely compiler-dependent consequences on code execution.
Pseudovariables
Ethereum provides the following pseudovariables and pseudoarrays:
•	block.prevhash - previous block hash
•	block.number - block number
•	block.timestamp - block timestamp
•	block.difficulty - block difficulty
•	block.coinbase - address of block miner
•	block.gaslimit - maximum amount of gas that can be spent in the block
•	tx.gasprice - amount paid by transaction for gas
•	tx.origin - original sender of transaction (NOT the sender of the current message, which is different in a nested call situation)
•	tx.gas - current amount of gas remaining
•	msg.datasize - length of the data provided by the message, measured as the number of complete 32-byte chunks
•	msg.sender - sender of the message
•	msg.value - value of the message
•	contract.balance - balance of the contract
•	msg.data[i] - the ith 32-byte chunk in message data
•	contract.storage[i] - the contract's long term storage, at index i
Functions
•	send(to, value, gas) - sends value ether to to, allowing the computation the given amount of gas.
•	x = msg(to, value, gas, datastart, datalen) - sends a message to to, using the data at memory indices datastart ... datastart+datalen*32-1, with the given amount of ether and gas, and sets x to the first 32 bytes of the result.
•	msg(to, value, gas, datastart, datalen, outputstart, outputlen) - sends a message toto, using the data at memory indicesdatastart ... datastart+datalen*32-1, with the given amount of ether and gas, and pastes the result to memory indicesoutputstart ... outputstart+outputlen*32-1`
•	x = create(endowment, gas, datastart, datalen) - creates a new contract using code from the given indices in memory as above, and return the address of the contract
•	x = sha3(v) - returns the SHA3 of the given 32-byte value
•	x = byte(y,z) - sets x to the zth byte of y
A lot of these commands require data to be loaded from memory; this is actually fairly easy to manage. Consider the following equivalent examples:
x = msg(0xf345747062de4d05d897d62c4696febbedcb36b8, 10^18, tx.gas - 100, [10,20,30], 3)
And:
a = array(3)
a[0] = 10
a[1] = 20
a[2] = 30
y = array(1)
msg(0xf345747062de4d05d897d62c4696febbedcb36b8, 10^18, tx.gas - 100, a, 3, y, 1)
x = y[0]
Conditionals and Loops
Best to learn by example:
if x > 5:
    y = 7
And:
x = 248
while x > 1:
    if (x % 2) == 0: 
        x = x / 2
    else:
        x = 3 * x + 1
Note that if x > 5: y = 7 on one line is invalid.
Comments
x = 4 //this is a comment and does not appear in the compiled code
  // this also is a comment on its own line
  /* this style is not supported however */
Halting
•	stop - this keyword by itself on one line stops execution
•	return(x) - returns the 32 bytes of value x
•	return(memstart, len) - returns the memory at indices memstart ... memstart+32*len-1
•	suicide(a) - destroys the contract, sending all remaining balance to a
Last edited by vbuterin, 3 days ago
