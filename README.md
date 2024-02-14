Lab X: Demo of Zero-knowledge Proofs(ZKPs) 
===

Introduction
---
Zero-knowledge proofs (ZKPs) are a family of probabilistic protocols, first described by [Goldwasser, Micali and Rackoff](https://people.csail.mit.edu/silvio/Selected%20Scientific%20Papers/Proof%20Systems/The_Knowledge_Complexity_Of_Interactive_Proof_Systems.pdf) in 1985.

One particular family of ZKPs is described as zero-knowledge Succinct Non-interactive ARguments of Knowledge, a.k.a. zkSNARKs. zkSNARKs are the most widely used zero-knowledge protocols, with the anonymous cryptocurrency Zcash and the smart-contract platform Ethereum among the notable early adopters.([cite](https://zokrates.github.io/introduction.html))

For more details on the background principles of ZPKs and zkSNARKs, please refer to the following materials.
- Vitalik Buterin’s introduction to SNARKs, part [1](https://medium.com/@VitalikButerin/quadratic-arithmetic-programs-from-zero-to-hero-f6d558cea649), [2](https://medium.com/@VitalikButerin/exploring-elliptic-curve-pairings-c73c1864e627), and [3](https://medium.com/@VitalikButerin/zk-snarks-under-the-hood-b33151a013f6)
- [What are zk-SNARKs?](https://z.cash/learn/what-are-zk-snarks/)
- [ZKP Science](https://zkp.science/)

In this lab, we will focus on the process of how a zk program was computed, proved, and verified, showcasing the active involvement of multiple parties, with the help of  [ZoKrates](https://github.com/Zokrates/ZoKrates?tab=readme-ov-file). Please read the [deliverable](#deliverable) before your hands-on.

For a better understanding, please view this figure first. (captured from [here](https://www.youtube.com/watch?v=sSlrywb5J_0&ab_channel=EthereumFoundation) at 10:10) 
Blue texts are the commands used in ZoKrates CLI.
![ZoKrates-CLI](https://github.com/ZhouYuxuan97/zk-demo/blob/main/ZoKrates-CLI.png)


Exercise 1. Setup and Hello-world
---

Follow the instructions in this [link](https://zokrates.github.io/gettingstarted.html), and install ZoKrates in MacOS or Linux by using `One-line installation` on the page. Then follow the instructions in 'Hello ZoKrates!' to execute the whole process. In each step, pay attention to which files are generated and match them to the above figure. After executing `zokrates export-verifier`, you will find a `verifier.sol` generated, deploy it to Remix IDE, and verify your proof in Remix.

Hints:
1. Don' t forget add ZoKrates to PATH as suggested 
    ```bash
    >>> export PATH=$PATH:/YOUR DIR/.zokrates/bin
    ```
2. The input paraments to the verifier.verifyTx() should be like these(for example):
    ```python
    proof: [[a[0],a[1]], [[b[0][0],b[0][1]],[b[1][0],b[1][1]]], [c[0],c[1]]]
    input: same as in input.json
    ```
    

Exercise 2. Prove Your Age to Others
---

In this exercise, we simulate a scenario where you are a prover who tries to prove that your age is bigger than a given number(21, in this case). You will pass your proof to a smart contract deployed by a verifier, while your age is not included in the proof. 

You should use a common .zok [file](https://github.com/ZhouYuxuan97/zk-demo/blob/main/comp.zok) to compile, and use a given setup([proving.key](https://github.com/ZhouYuxuan97/zk-demo/blob/main/proving.key)) to replace the command of `zokrates setup`. 

Using the given [verifier.sol](https://github.com/ZhouYuxuan97/zk-demo/blob/main/verifier.sol) smart contract to Remix IDE and put your proof arguments to `verifyTx` function. 

Hint:
1. You could use this command to download the files to avoid font/format issues, replace the link to adjust other files  
    ```bash
    >>> wget -O proving.key https://github.com/ZhouYuxuan97/zk-demo/blob/main/proving.key?raw=true
    ```

Exercise 3 (Additional). Prove Your Knowledge of a Hash Preimage 
---

In this exercise, we simulate a scenario where Alice tries to prove she knows a hash preimage for a digest chosen by Bob. Samely, the preimage wouldn’t be revealed when Bob verifies the proof.

To start with, you can create a new file named `generate-hash.zok` to learn about how to generate hash in `.zok`:
```
import "hashes/sha256/512bitPacked" as sha256packed;

def main(private field a, private field b, private field c, private field d) -> field[2] {
    field[2] h = sha256packed([a, b, c, d]);
    return h;
}
```

The first line imports the `sha256packed` function from the ZoKrates standard library.

`sha256packed` is a SHA256 implementation that is optimized for the use in the ZoKrates DSL. Here is how it works: We want to pass 512 bits of input to SHA256. However, a `field` value can only hold 254 bits due to the size of the underlying prime field we are using. As a consequence, we use four field elements, each one encoding 128 bits, to represent our input. The four elements are then concatenated in ZoKrates and passed to SHA256. Given that the resulting hash is 256 bit long, we split it in two and return each value as a 128 bit number.

Then compile generate-hash.zok and create a witness file, record the output.

Now, based on the code snippet in `generate-hash.zok` and witness output, Bob needs to design a `prove-preimage.zok`, compile it, make a setup and export `verifier.sol`, and deploy `verifier.sol` to Remix IDE. Alice is going to compile the `prove-preimage.zok` as well, enter her preimage to generate witness, using Bob's `proving.key` to construct the proof, then put the arguments in proof to `verifier.verifyTx()` to show her knowledge of that hash preimage to Bob.
 
Help Bob to design `prove-preimage.zok` and follow these steps to finish the demo.
 
 Hints:
1. You could use this command to generate witness of `generate-hash`
    ```bash
    >>> zokrates compute-witness -a 0 1 2 4 --verbose
    ```
 
## Deliverable

---
1. You should create separate folders for all exercises. 
2. Submission should be a pdf file.
3. For all exercises, you should submit the screenshots of what files remain in your folder; the screenshots of the terminal showing what commands are executed and their outputs; the screenshots of your contract executing inputs and results in Remix IDE.
4.For all exercises, copy your code in `.zok` files and proofs in `proof.json` to the pdf submission.
3. For Exercise 3, create two folders called `Alice` and `Bob` individually, follow the instructions and understand the scenario(difference in roles' duties), and submit the screenshots of what files remain in `Alice` and `Bob` folders.
