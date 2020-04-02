
# Section 0️⃣: The Golden Rule

<br/>

## ⚪️ 0 The Golden Rule: Design for lean testing

:white_check_mark: **Do:**
Testing code is not like production-code - design it to be dead-simple, short, abstraction-free, flat, delightful to work with, lean. One should look at a test and get the intent instantly.

Our minds are full with the main production code, we don't have 'headspace' for additional complexity. Should we try to squeeze yet another challenging code into our poor brain it will slow the team down which works against the reason we do testing. Practically this is where many teams just abandon testing.

The tests are an opportunity for something else - a friendly and smiley assistant, one that it's delightful to work with and delivers great value for such a small investment. Science tells us that we have two brain systems: system 1 is used for effortless activities like driving a car on an empty road and system 2 which is meant for complex and conscious operations like solving a math equation. Design your test for system 1, when looking at test code it should _feel_ as easy as modifying an HTML document and not like solving 2X(17 × 24).

This can be achieved by selectively cherry-picking techniques, tools and test targets that are cost-effective and provide great ROI. Test only as much as needed, strive to keep it nimble, sometimes it's even worth dropping some tests and trade reliability for agility and simplicity.

![alt text](/assets/headspace.png "We have no head room for additional complexity")

Most of the advice below are derivatives of this principle.

### Ready to start?

#### [`Section 1: The Test Anatomy`](./section-1-the-test-anatomy)
