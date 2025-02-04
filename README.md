# GoLogicFlaw - Google CTF 2024 (Beginner's Quest)

This challenge involves exploiting a logic flaw in Go's random number generation. The server generates a "random" number using `rand.Seed(num)`, but the seed is derived from the server's time. This makes it predictable.

## Understanding the Vulnerability
In the challenge, the server prints a number inside square brackets, like `[1738694999]`. This number is actually the server's timestamp used as a seed for Go's random number generator. Since `rand.Seed(num)` always produces the same sequence of numbers for a given seed, we can predict the output.

## Exploitation Steps
1. **Connect to the server:**
   ```sh
   nc go-logic-flaw.2024-bq.ctfcompetition.com 1337
   ```
2. **Note the timestamp** displayed in square brackets `[XXXXXX]`.
3. **Modify and run the following Go program**, replacing `SEED_FROM_SERVER` with the observed timestamp.

## Predictor Script
```go
package main

import (
	"fmt"
	"log"
	"math/rand"
	"os"
)

func main() {
	rand.Seed(SEED_FROM_SERVER) // Replace with the server-provided seed

	file, _ := os.OpenFile("log.txt", os.O_APPEND|os.O_CREATE|os.O_WRONLY, 0644)
	defer file.Close()

	logger := log.New(file, "", log.LstdFlags)
	num := rand.Intn(1000000000)

	logger.Printf("Predicted number: %d", num)
	fmt.Printf("Predicted number: %d\n", num)
}
```

4. **Run the script** in an online Go compiler or locally.
5. **Submit the predicted number** to the server to solve the challenge.
6. Flag is CTF{B3c@us3T1m1ngM@tt3rs}

## Notes
- The Go random number generator is **not cryptographically secure** and can be exploited when the seed is known.
- This technique works because `rand.Seed(num)` always produces the same sequence for a fixed seed.


