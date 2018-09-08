## Software Design Studio -John Ousterhout

### [A Philosophy of Software Design|Talking at Google][gtalk]

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
	- <font color=red>Working code isn't enough: must minimize complexity</font>
	- Complexity comes from dependencies and obscurity(朦胧)
	- <font color=red>Strategic(战略) vs.tatical(战术) programming</font>
	- <font color=red>[Classes should be deep][inf_hiding]</font>
	- General-purpose classes are deeper
	- New layer, new abstraction
	- Comments should describe things that are not obvious from the code
	- <font color=red>Define errors out of existence</font>
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
+ <font color=red>Length isn't the big issue, it's abstraction.</font>

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
+ <font color=red>Better approach: define semantics to eliminate exceptions</font>
+ Example mistakes:
	- Tcl unset command
		(throws exception if variable doesn't exist)
	- Windows: can't delete file if open
	> In Unix, if you delete a file while it's open, what happens is it deletes the file from the directory and from the namespace. It no longer appears anywhere in the file system. But actual contents of this file still hang around, so that any process that's using the file can continue to access the file.And then when the last open instance of the file is closedl, then finally it cleans up and throws away everything else.
	- Java substring range exceptions
+ <font color=blue>Overral goal: minimize the number of places where exceptions must be handled.</font>

#### Tactical vs. Strategic Programming
+ Tactical programming
	- Goal: get next feature/bug fix working ASAP
	- A few shortcuts and kludges are OK?
	- Result: bad design, high complexity
	- Extreme: tactical tornadoes
+ Complexity is incremental

[gtalk]:https://www.youtube.com/watch?v=bmSAYlu0NcY
[cs190]:https://web.stanford.edu/~ouster/cgi-bin/cs190-winter18/index.php
[inf_hiding]:https://cs.uwaterloo.ca/~dberry/COURSES/software.engr/lectures.pdf/inf_hiding.pdf
[csbd]: