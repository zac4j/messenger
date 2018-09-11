## Software Design Studio -John Ousterhout

### [A Philosophy of Software Design|Talks at Google][gtalk]

#### Problem decomposition
There question:
+ Is it possible to be taught?
	- Yes.
+ Who's going to do it?
	- Not most of today's faculty, but John himself.
+ How do you do it?
	- Give it a try, [CS190 Software Design Studio][cs190].

#### What are the Secrets?
+ A few (somewhat vague) overall concepts:
	- **Working code isn't enough: must minimize complexity**
	- Complexity comes from dependencies and obscurity(朦胧)
	- **Strategic(战略) vs.tatical(战术) programming**
	- **[Classes should be deep][inf_hiding]**
	- General-purpose classes are deeper
	- New layer, new abstraction
	- Comments should describe things that are not obvious from the code
	- **Define errors out of existence**
	- Pull complexity downwards

#### Class should be deep?
![class_should_be_deep][csbd]

**Typical shadow method**

``` java
private void addNullValueForAttribute(String attr) {
	data.put(attr, null);
}
```

+ **Common wisdom:"classes and methods should be small", result: classitis**
``` java
FileInputStream fileStream = new FileInputStream(filename);
BufferedInputStream bufferedStream = new BufferedInputStream(fileStream);
ObjectInputStream objectStream = new ObjectInputStream(bufferedStream);
```
+ Length isn't the big issue, it's abstraction.

**A Deep Interface**
+ Unix file I/O
	- int open(const char* path, int flags, mode_t permissions);
	- int close(int fd);
	- ssize_t read(int fd, void* buffer, size_t count);
	- ssize_t write(int fd, const void* buffer, size_t count);
	- off_t lseek(int fd, off_t offset, int referencePosition);
+ Hidden below the interface:
	- On-disk representation, disk block allocation
	- Directory management, path loopup
	- Permission management
	- Disk scheduling
	- Block caching
	- Device independence

#### Define Errors Out of Existence
+ Exceptions: a huge source of complexity
+ **Common wisdom: detect and throw as many errors as possible**
+ **Better approach: define semantics to eliminate exceptions**
+ Example mistakes:
	- Tcl unset command
		(throws exception if variable doesn't exist)
	- Windows: can't delete file if open
	> In Unix, if you delete a file while it's open, what happens is it deletes the file from the directory and from the namespace. It no longer appears anywhere in the file system. But actual contents of this file still hang around, so that any process that's using the file can continue to access the file.And then when the last open instance of the file is closedl, then finally it cleans up and throws away everything else.
	- Java substring range exceptions
+ **Overral goal: minimize the number of places where exceptions must be handled.**

#### Tactical vs. Strategic Programming

![tactical_vs_strategic][tvs]

**Tactical programming**
	- Goal: get next feature/bug fix working ASAP
	- A few shortcuts and kludges are OK?
	- Result: bad design, high complexity
	- Extreme: tactical tornadoes
+ Complexity is incremental
+ Working code isn't enough

**Strategic programming**
	- Goal:produce a great design
	- Simplify future development
	- Minimize complexity
	- Must sweat the small stuff
+ Investment mindset
	- Take extra time today
	- Pays back in the long run

**How Much To Invest?**
+ Most startups are totally tactical
	- Pressure to get first products out quickly
		- "We can clean this up later"
	- Code base quickly turns to spaghetti(意面)
	- Extremely diffcult/expensive to repair damage
+ Facebook: "Move quickly and break things"
	- Empowered developers
	- Code base notoriously incomprehensible/unstable
	- Eventually changed to "Move quickly with solid infrastructure"
+ Can succeed with strong design culture:Google and VMware
	- Attracted best engineers

+ Make continual small investments: 10-20% overhead
+ When writing new code
	- Careful design
	- Good documentation
+ When changing existing code
	- Always find something to improve
	- Don't settle for(满足于) fewest modified lines of code
	- Goal: after change, system is the way it would have been if designed that way from the start

#### Is the Course Working?
+ **Just the first step towards becoming a great prmgrammer**
+ **Good energy in class:**
	- Tone of discussions changes halfway through
	- Students are thinking about their code in new ways
+ **Interesting challenges for me:**
	- What causes complexity?
	- How to design simple code?
+ **Discovering new ideas from reading students' code**
	- Specialized -> complicated
	- General-purpose -> simple, deep

[gtalk]:https://www.youtube.com/watch?v=bmSAYlu0NcY
[cs190]:https://web.stanford.edu/~ouster/cgi-bin/cs190-winter18/index.php
[inf_hiding]:https://cs.uwaterloo.ca/~dberry/COURSES/software.engr/lectures.pdf/inf_hiding.pdf
[csbd]:http://o6mq6hyfb.bkt.clouddn.com/class_should_be_deep.PNG
[tvs]:http://o6mq6hyfb.bkt.clouddn.com/tactical_vs_strategic.PNG
