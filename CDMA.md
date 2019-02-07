We got a bit confused talking about CDMA when I tried to use a vector example, so here are two worked out examples with vectors so you can see why we need to divide by the number of dimensions at the end to get your message back.

Example 1: Using orthogonal vectors with only 1 non-zero dimension:
- Consider two orthogonal vectors j (0,1) and i (1,0)
- A wants to send the message 2 and B wants to send the message 3
- A is assigned vector j and B is assigned vector i
- A multiplies 2 by j to get (0,2)
- B multiplies 3 by i to get (3,0)
- These are additively combined during transmission to create the vector (3,2)
- The receiver wants to recover A's message, so performs the dot product of the received vector (3,2) with j
  - (3,2) dot (0,1) = (3\*0) + (2\*1) = 2
  - It appears I don't need to divide by anything here.  that's because I only projected A's value into a single dimension.
  
Example 2: Using orthogonal vectors with 2 non-zero dimensions:
- Consider two orthogonal vectors j (1, 1) and i (1,-1) (note the dot product is (1\*1) + (1\*-1) = 0)
  - We're going to project our messages into two dimensions now
- A wants to send the message 2 and B wants to send the message 3
- A transmits 2 \* (1,1) = (2,2)
- B transmits 3 \* (1,-1) = (3,-3)
- Combined transmission: (5,-1) (pretty interesting now...)
- Now we want to recover A's message again by taking the dot product of the transmitted message with j:
  - (5,-1) dot (1,1) = (5\*1) + (-1\*1) = 5 - 1 = 4.  But that's not our message!
  - To recover our message we need to divide by the number of dimensions onto which we projected (i.e., 2)
  - 4/2 = 2, which is A's original message
- Does it work for B's message?
  - (5,-1) dot (1,-1) = 5 + 1 = 6.
  - 6/2 = 3.
  - Yes.
- This is kind of intuitive if we see that when we projected A onto j, we have the full magnitude of A in all non zero dimensions of j, so it makes sense that recovering the original A requires dividing by the number of dimensions.

CDMA works in the same way.
- You project your message onto a n-dimensional chipping code.
- All the transmissions get added together.
- To recover, you multiply the combined message by the chipping code and divide by the number of dimensions.
