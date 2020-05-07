# How can a pool prevent participants from stealing or not working?

When you are in a mining pool, you install special software on your computer for work sharing.
It's not as easy as promising you'll do a certain set of the nonces---then you could get away with doing nothing.
The software verifies you are working.
Not sure how, maybe random controls?

If you find a solution locally, then you still can't steal the money, because the coinbase is part of the block header.
You can't change the coinbase to an address you control.
It's also possible for the pool to withhold the coinbase, so you don't even know what block header you are working on, just its hash (or whatever intermediate data you need to finish the block header hash).

One possible attack is for a pool miner to be disruptive, and withholding successful PoW. 
It's not a very strong attack, because its power is proportional to your share in the pool.
