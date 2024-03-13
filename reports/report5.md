# Lab Report 5 - Putting it All Together (Week 9) {#week-9-lab-report}
In this blog, we would like to explain how to fix the bug in a program while utilizing the community service like EdStem. In the end, we would add a note on the reflection of the course, CSE15L.

## Debugging Scenario
In this section, we would show the example of the conversation in [Edstem](https://edstem.org/us/dashboard) to explain what kind of communication can help you figure out how to fix the bug you are struggling with.

### Setup
Before going intot the example, we would show the step up for the program a student is working on.
(Used code from CSE12)

The assignment is as following.
> 1. Implement the code to merge to sorted ArrayList into one while keeping entire array order for mergesort.
> Ex. merge([1, 5, 9], [2, 6, 7]) returns [1, 2, 5, 6, 7, 9]
> 2. You have to write bash script (test.sh) to compile the code and run the test (TestMerge.java [provided]).

### Student Post 1
> My code does not pass the test for merge method.
> I would show my outputs from test and my program files, `Merge.java`, `TestMerge.java`, and `test.sh`.

**File & Directory Structure**
Firstly, my folder structure is following.
```
- lib
  - hamcrest-core-1.3.jar
  - junit-4.13.2.jar
- Merge.class
- Merge.java
- test.sh
- TestMerge.class
- TestMerge.java
```

**Merge.java**
```java
import java.util.ArrayList;
import java.util.List;

public class Merge {
  static List<Integer> merge(List<Integer> array1, List<Integer> array2) {
    int i = 0;
    int j = 0;
    List<Integer> result = new ArrayList<Integer>();
    while (i < array1.size() && j < array2.size()) {
      if (array1.get(i) <= array2.get(j)) {
        result.add(array1.get(i));
        i += 1;
      } else {
        result.add(array2.get(j));
        j += 1;
      }
    }
    while (i < array1.size()) {
      result.add(array1.get(i));
      i += 1;
    }
    while (j < array2.size()) {
      result.add(array2.get(j));
      i += 1;
    }
    return result;
  }
}
```

**TestMerge.java**
```java
import static org.junit.Assert.*;
import org.junit.*;
import java.util.Arrays;
import java.util.List;

public class TestMerge {
  @Test
  public void testMerge1() {
    List<Integer> array1 = Arrays.asList(1, 2, 3, 4);
    List<Integer> array2 = Arrays.asList(5, 6, 7, 8);
    List<Integer> expected = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8);
    List<Integer> filtered = Merge.merge(array1, array2);
    assertEquals(expected, filtered);
  }

  @Test
  public void testMerge2() {
    List<Integer> array1 = Arrays.asList(5, 6, 7, 8);
    List<Integer> array2 = Arrays.asList(1, 2, 3, 4);
    List<Integer> expected = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8);
    List<Integer> filtered = Merge.merge(array1, array2);
    assertEquals(expected, filtered);
  }

  @Test
  public void testMerge3() {
    List<Integer> array1 = Arrays.asList(1, 3, 5, 7);
    List<Integer> array2 = Arrays.asList(2, 4, 6, 8);
    List<Integer> expected = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8);
    List<Integer> filtered = Merge.merge(array1, array2);
    assertEquals(expected, filtered);
  }

  @Test
  public void testMerge4() {
    List<Integer> array1 = Arrays.asList(1, 3, 4, 5, 7);
    List<Integer> array2 = Arrays.asList(2, 6, 8);
    List<Integer> expected = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8);
    List<Integer> filtered = Merge.merge(array1, array2);
    assertEquals(expected, filtered);
  }
}
```

**test.sh**
```bash
javac -cp ./lib/junit-4.13.2.jar:../lib/hamcrest-core-1.3.jar:. *.java
java -cp ./lib/junit-4.13.2.jar:./lib/hamcrest-core-1.3.jar:. org.junit.runner.JUnitCore TestMerge
```

I tried the following command.
```
bash test.sh
```

My program&'s symptom is it does not finish the test.
```
JUnit version 4.13.2
.
```
This continues even though I wait for a while.

### TA Post 1
There are a few points you can fix first to identify the bug.
1. Include `set -e` in `test.sh`.
   This line can stop the bash script if it encounter the erorr. Therefore, this makes it clear whether this error comes from when compiling or running the program.
2. Set timeout for test cases.
  By adding `@Test(timeout = 500)` above the test method header, you can set a time limit for the test cases. This makes test cases fail if it takes too much time. In your case, this might help you to identify which test case you failed.
3. Use jdb to figure out which parts the program takes time.
   As the program does not finish, this bug most likely to come from while loop. To investigate the bug in a while loop, it is a good idea to use jdb. When it stops working you can use `suspend` to stop it.
   
### Student Post 2
1. Inclue `set -e`
   Firstly, I included `set -e`, but output does not change, I think this indicates that compile successfully finished and error was caused when running the program.

```bash
set -e

javac -cp ./lib/junit-4.13.2.jar:../lib/hamcrest-core-1.3.jar:. TestMerge.java
java -cp ./lib/junit-4.13.2.jar:./lib/hamcrest-core-1.3.jar:. org.junit.runner.JUnitCore TestMerge
```

2. Include `@Test(timeout = 500)`
   I ran the test again while including timeout. I got the different output saying I failed 3 tests out of four.

``` java
import static org.junit.Assert.*;
import org.junit.*;
import java.util.Arrays;
import java.util.List;

public class TestMerge {
  @Test(timeout = 500)
  public void testMerge1() {
    List<Integer> array1 = Arrays.asList(1, 2, 3, 4);
    List<Integer> array2 = Arrays.asList(5, 6, 7, 8);
    List<Integer> expected = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8);
    List<Integer> filtered = Merge.merge(array1, array2);
    assertEquals(expected, filtered);
  }

  @Test(timeout = 500)
  public void testMerge2() {
    List<Integer> array1 = Arrays.asList(5, 6, 7, 8);
    List<Integer> array2 = Arrays.asList(1, 2, 3, 4);
    List<Integer> expected = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8);
    List<Integer> filtered = Merge.merge(array1, array2);
    assertEquals(expected, filtered);
  }

  @Test(timeout = 500)
  public void testMerge3() {
    List<Integer> array1 = Arrays.asList(1, 3, 5, 7);
    List<Integer> array2 = Arrays.asList(2, 4, 6, 8);
    List<Integer> expected = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8);
    List<Integer> filtered = Merge.merge(array1, array2);
    assertEquals(expected, filtered);
  }

  @Test(timeout = 500)
  public void testMerge4() {
    List<Integer> array1 = Arrays.asList(1, 3, 4, 5, 7);
    List<Integer> array2 = Arrays.asList(2, 6, 8);
    List<Integer> expected = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8);
    List<Integer> filtered = Merge.merge(array1, array2);
    assertEquals(expected, filtered);
  }
}
```

Even though this information was helpful, this did not help me to figure out what the bug is, so I tried the third one.

3. Use `jdb`

First I compiled with `-g` option.
```bash
javac -g -classpath ./lib/junit-4.13.2.jar:../lib/hamcrest-core-1.3.jar:. *.java
```
```bash
jdb -classpath ./lib/junit-4.13.2.jar:./lib/hamcrest-core-1.3.jar:. org.junit.runner.JUnitCore TestMerge
```
I ran this command and typed `run`, then type `suspend`. Then run `threads` to show all threads and select `thread 1` to get in the thread.
To find out which line the program was running, typed `where`.
Then I got the following.

```
main[1] where 
  [1] java.util.Arrays.copyOf (Arrays.java:3,512)
  [2] java.util.Arrays.copyOf (Arrays.java:3,481)
  [3] java.util.ArrayList.grow (ArrayList.java:237)
  [4] java.util.ArrayList.grow (ArrayList.java:244)
  [5] java.util.ArrayList.add (ArrayList.java:454)
  [6] java.util.ArrayList.add (ArrayList.java:467)
  [7] Merge.merge (Merge.java:23)
  [8] TestMerge.testMerge1 (TestMerge.java:12)
```

This indicates the program was running at line 23 in `Merge.java`, which is `result.add(array2.get(j));` in a while loop as expected.

So I tried to look at variables like `i` and `j`.
However, it said it was null.
```
main[1] print i
com.sun.tools.example.debug.expr.ParseException: Name unknown: i
 i = null
main[1] print j
com.sun.tools.example.debug.expr.ParseException: Name unknown: j
 j = null
main[1] locals 
Method arguments:
original = instance of java.lang.Object[157704907] (id=1000)
newLength = 236557360
newType = instance of java.lang.Class(reflected class=java.lang.Object[], id=363)
Local variables:
```
Therefore, I used `step` to go next line then print variables again.
```
main[1] step
> 
Step completed: "thread=main", Merge.merge(), line=23 bci=171
23          result.add(array2.get(j));

main[1] step
> 
Step completed: "thread=main", Merge.merge(), line=24 bci=172
24          i += 1;

main[1] print i
 i = 157704907
main[1] print j
 j = 0
```
I realized that I mistakenly updated `i` not `j`.

So I fixed the line 24 `i += 1;` to `j += 1;`
Then finally it passed all the test cases.

```
bash test.sh
```
```
JUnit version 4.13.2
....
Time: 0.006

OK (4 tests)
```
### Overview of the Program.

**File & Directory Structure**
```
- lib
  - hamcrest-core-1.3.jar
  - junit-4.13.2.jar
- Merge.class
- Merge.java
- test.sh
- TestMerge.class
- TestMerge.java
```

**test.sh (Before Fixing the Bug)**
```
javac -cp ./lib/junit-4.13.2.jar:../lib/hamcrest-core-1.3.jar:. *.java
java -cp ./lib/junit-4.13.2.jar:./lib/hamcrest-core-1.3.jar:. org.junit.runner.JUnitCore TestMerge
```

**Merge.java (Before Fixing the Bug)**
```java
import java.util.ArrayList;
import java.util.List;

public class Merge {
  static List<Integer> merge(List<Integer> array1, List<Integer> array2) {
    int i = 0;
    int j = 0;
    List<Integer> result = new ArrayList<Integer>();
    while (i < array1.size() && j < array2.size()) {
      if (array1.get(i) <= array2.get(j)) {
        result.add(array1.get(i));
        i += 1;
      } else {
        result.add(array2.get(j));
        j += 1;
      }
    }
    while (i < array1.size()) {
      result.add(array1.get(i));
      i += 1;
    }
    while (j < array2.size()) {
      result.add(array2.get(j));
      i += 1;
    }
    return result;
  }
}
```

**TestMerge.java  (Before Fixing the Bug)**
```java
import static org.junit.Assert.*;
import org.junit.*;
import java.util.Arrays;
import java.util.List;

public class TestMerge {
  @Test
  public void testMerge1() {
    List<Integer> array1 = Arrays.asList(1, 2, 3, 4);
    List<Integer> array2 = Arrays.asList(5, 6, 7, 8);
    List<Integer> expected = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8);
    List<Integer> filtered = Merge.merge(array1, array2);
    assertEquals(expected, filtered);
  }

  @Test
  public void testMerge2() {
    List<Integer> array1 = Arrays.asList(5, 6, 7, 8);
    List<Integer> array2 = Arrays.asList(1, 2, 3, 4);
    List<Integer> expected = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8);
    List<Integer> filtered = Merge.merge(array1, array2);
    assertEquals(expected, filtered);
  }

  @Test
  public void testMerge3() {
    List<Integer> array1 = Arrays.asList(1, 3, 5, 7);
    List<Integer> array2 = Arrays.asList(2, 4, 6, 8);
    List<Integer> expected = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8);
    List<Integer> filtered = Merge.merge(array1, array2);
    assertEquals(expected, filtered);
  }

  @Test
  public void testMerge4() {
    List<Integer> array1 = Arrays.asList(1, 3, 4, 5, 7);
    List<Integer> array2 = Arrays.asList(2, 6, 8);
    List<Integer> expected = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8);
    List<Integer> filtered = Merge.merge(array1, array2);
    assertEquals(expected, filtered);
  }
}
```


**Full Commands I ran**
Bash
```bash
bash test.sh
bash test.sh // after adding set -e
bash test.sh // after adding timeout
javac -g -classpath ./lib/junit-4.13.2.jar:../lib/hamcrest-core-1.3.jar:. *.java
jdb -classpath ./lib/junit-4.13.2.jar:./lib/hamcrest-core-1.3.jar:. org.junit.runner.JUnitCore TestMerge
bash test.sh // after fixing the bug
```
jdb
```
run
suspend
threads
thread 1
where
print i
print j
locals
step
print i
print j
locals
```
**test.sh (After Fixing the Bug)**
```
set -e

javac -cp ./lib/junit-4.13.2.jar:../lib/hamcrest-core-1.3.jar:. *.java
java -cp ./lib/junit-4.13.2.jar:./lib/hamcrest-core-1.3.jar:. org.junit.runner.JUnitCore TestMerge
```

**Merge.java (After Fixing the Bug)**
```java
import java.util.ArrayList;
import java.util.List;

public class Merge {
  static List<Integer> merge(List<Integer> array1, List<Integer> array2) {
    int i = 0;
    int j = 0;
    List<Integer> result = new ArrayList<Integer>();
    while (i < array1.size() && j < array2.size()) {
      if (array1.get(i) <= array2.get(j)) {
        result.add(array1.get(i));
        i += 1;
      } else {
        result.add(array2.get(j));
        j += 1;
      }
    }
    while (i < array1.size()) {
      result.add(array1.get(i));
      i += 1;
    }
    while (j < array2.size()) {
      result.add(array2.get(j));
      j += 1;
    }
    return result;
  }
}

```

**TestMerge.java  (After Fixing the Bug)**
```java
import static org.junit.Assert.*;
import org.junit.*;
import java.util.Arrays;
import java.util.List;

public class TestMerge {
  @Test(timeout = 5000)
  public void testMerge1() {
    List<Integer> array1 = Arrays.asList(1, 2, 3, 4);
    List<Integer> array2 = Arrays.asList(5, 6, 7, 8);
    List<Integer> expected = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8);
    List<Integer> filtered = Merge.merge(array1, array2);
    assertEquals(expected, filtered);
  }

  @Test(timeout = 5000)
  public void testMerge2() {
    List<Integer> array1 = Arrays.asList(5, 6, 7, 8);
    List<Integer> array2 = Arrays.asList(1, 2, 3, 4);
    List<Integer> expected = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8);
    List<Integer> filtered = Merge.merge(array1, array2);
    assertEquals(expected, filtered);
  }

  @Test(timeout = 5000)
  public void testMerge3() {
    List<Integer> array1 = Arrays.asList(1, 3, 5, 7);
    List<Integer> array2 = Arrays.asList(2, 4, 6, 8);
    List<Integer> expected = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8);
    List<Integer> filtered = Merge.merge(array1, array2);
    assertEquals(expected, filtered);
  }

  @Test(timeout = 5000)
  public void testMerge4() {
    List<Integer> array1 = Arrays.asList(1, 3, 4, 5, 7);
    List<Integer> array2 = Arrays.asList(2, 6, 8);
    List<Integer> expected = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8);
    List<Integer> filtered = Merge.merge(array1, array2);
    assertEquals(expected, filtered);
  }
}
```

# Reflection
I feel learning about the general procedure of debugging.
Even though I had a background of writing a program, I only used `print` statement to check variable. Otherwise, I make changes back until it starts working properly again.
After learning writing test cases to induce the errors and using jdb to figure out the change of local variables, I feel I could attain more tools to effectly address bugs.

# Citation
I used ChatGPT to figure out how to print out local variables.
```
I'm using jdb for debugging.
The program does not end while loop, so I used suspend and then use print and locals.
However, it says local variable is null. What is the reason why I'm getting this?
```

>When you encounter an issue where `jdb` (the Java Debugger) reports that a local variable is null during debugging, especially when you're trying to inspect variables within a loop that doesn't terminate as expected, there are a few potential reasons to consider:
>
>1. **Scope of Variable**: Ensure that the variable you are trying to inspect is in the scope at the point where you've suspended the execution with `suspend`. Variables outside the current scope or not yet initialized in the code execution path will not be visible or may appear as null.
>2. **Optimization by the JVM**: The Java Virtual Machine (JVM) performs optimizations that may affect debugging. For example, the JVM might optimize away variables that are not used beyond a certain point, leading to them appearing as null in the debugger. Make sure to compile your Java program with debugging information enabled and optimizations disabled. This is typically done with the `-g` flag during compilation (`javac -g YourClass.java`).
>3. **Timing of Inspection**: If you're inspecting the variable immediately after a `suspend` command, verify that the suspension occurred at a point in your program where the variable you're interested in was already initialized and in scope. Use breakpoints or conditional suspensions to accurately pause the program where relevant.
>4. **Thread-Specific Data**: If your application is multi-threaded, ensure you're inspecting the variable in the correct thread context. Variables may have different states or values in different threads, or might not be initialized yet in the thread you are inspecting.
>5. **Debugger and JVM Compatibility**: Ensure that the version of `jdb` you're using is compatible with the version of the JVM running your application. Incompatibilities might lead to unexpected behavior when inspecting variables.
>6. **Correct Use of Commands**: Double-check the syntax and usage of `print` and `locals` commands in `jdb`. For example, you should use `print myVariable` to inspect the value of `myVariable`, and `locals` to list all local variables in the current scope. Mistakes in these commands could lead to misleading outputs.
>
>To troubleshoot further, you might:
>- Place additional breakpoints before the loop to inspect the variable's state leading up to the loop.
>- Ensure that your code is compiled with the `-g` flag to include all debugging information.
>- Use `where` or `wherei` commands in `jdb` to check the current execution point and call stack to ensure you are in the correct context when inspecting variables.
>
>By methodically checking these areas, you should be able to pinpoint the reason why a local variable is appearing as null in your debugging session.
>