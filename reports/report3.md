# Lab Report 3 Servers and SSH Key (Week 5) {#week-5-lab-report}
This blog have two sections `How to Fix a Bug` and `How to Utilize find Command`.

# How to Fix a Bug
In this section, we explain how to fix a bug by using the example below:
This code has a bug and we take step by step to figure out how to fix it as following:

1. Observe Symptoms of the Program
2. Find out Cause of the Bug
3. Fix the Program


This program takes `arr` array of itegers as an input and reverse the array itself. As `arr` is a reference of the object, by directly editing `arr`, you can modify the original array, which is the reason why you don't have to return the updated array. (This is the `inplace` modification.)

``` java
public class ArrayExamples {
  static void reverseInPlace(int[] arr) {
    for(int i = 0; i < arr.length; i += 1) {
      arr[i] = arr[arr.length - i - 1];
    }
  }
}
```

## Observe Symptoms of the Program
First step is to find out the symtoms of this program. By giving two types of input (`failure-inducing input` and `inputs that doesn't induce a failure`), we are going to find out when this program works and does not.
We implement this idea by using the `JUnit` (testing library for Java.)

### Failure-Inducing Inputs
``` java
@Test
public void testReverseInPlace1() {
  int[] input = { 1, 2 };
  ArrayExamples.reverseInPlace(input);
  assertArrayEquals(new int[] { 2, 1 }, input);
}

@Test
public void testReverseInPlace2() {
  int[] input = { 1, 2, 3 };
  ArrayExamples.reverseInPlace(input);
  assertArrayEquals(new int[] { 3, 2, 1 }, input);
}
```
We use two arrays as examples with 2 and 3 length respectively. By using `assertArratEquals([expected], [actual])`, we can test whether the program output the expected array.
As the test stops once the discrepancy was found, we need to define two test methods.

``` bash
JUnit version 4.13.2
.E.E
Time: 0.004
There were 2 failures:
1) testReverseInPlace1(ArrayTests)
arrays first differed at element [1]; expected:<1> but was:<2>
==============================================================
2) testReverseInPlace2(ArrayTests)
arrays first differed at element [2]; expected:<1> but was:<3>
==============================================================

FAILURES!!!
Tests run: 2,  Failures: 2
```
(As output is too long we cut unneccesary parts with `=`)
As expected, this test case failed. JUnit outputs what was expected and what was given. In this case, for first array, the last element (index 1) was actually `2` not `1` as expected.
For the second array, the last element (index 2) was actually `3` not `1` as expected.

Based on this result, we found this program output was
* 1st arr: `arr = {2, 2}`
* 2nd arr: `arr = {3, 2, 3}` (not information about the last element.)

### Inputs that doesn't Induce a Failure

``` java
@Test
public void testReverseInPlace() {
  int[] input1 = {};
  ArrayExamples.reverseInPlace(input1);
  assertArrayEquals(new int[] {}, input1);

  int[] input2 = { 1 };
  ArrayExamples.reverseInPlace(input2);
  assertArrayEquals(new int[] { 1 }, input2);
}
```
We use two arrays as examples with 0 and 1 length respectively. 
```
JUnit version 4.13.2
.
Time: 0.003

OK (1 test)
```
This test successfully ran without any errors.

## Find out Cause of the Bug
In the next step, we are going to find out the reasons of this symtopms.
Based on the observation of the symtopms above, we can speculate that this program only works for no elements array or 1 element array and doesn't work for more than one elements arrays.

In the program, we use below to update the memory pointer of the array.
``` java
for(int i = 0; i < arr.length; i += 1) {
  arr[i] = arr[arr.length - i - 1];
}
```
Think about the example of two elements.

### $\{1, 2\}$
* `i = 0`:
  * Before: arr = {1, 2}
  * arr[arr.length - i - 1] ->  arr[2 - 0 - 1] -> arr[1] -> 2
  * arr[i] = arr[arr.length - i - 1] -> arr[0] = 2
  * After: arr = {2, 2}
* `i = 1`:
  * Before: arr = {2, 2}
  * arr[arr.length - i - 1] ->  arr[2 - 1 - 1] -> arr[0] -> **2**
  * arr[i] = arr[arr.length - i - 1] -> **arr[1] = 2**
  * After: arr = {2, 2}
  
### $\{1, 2, 3\}$
* `i = 0`:
  * Before: arr = {1, 2, 3}
  * arr[arr.length - i - 1] ->  arr[3 - 0 - 1] -> arr[2] -> 3
  * arr[i] = arr[arr.length - i - 1] -> arr[0] = 3
  * After: arr = {3, 2, 3}
* `i = 1`:
  * Before: arr = {3, 2, 3}
  * arr[arr.length - i - 1] ->  arr[3 - 1 - 1] -> arr[1] -> 2
  * arr[i] = arr[arr.length - i - 1] -> arr[1] = 2
  * After: arr = {3, 2, 3}
* `i = 2`:
  * Before: arr = {3, 2, 3}
  * arr[arr.length - i - 1] ->  arr[3 - 2 - 1] -> arr[0] -> **3**
  * arr[i] = arr[arr.length - i - 1] -> **arr[2] = 3**
  * After: arr = {3, 2, 3}

Based on this, we figured out two problems in this code.
1. We don't have to loop `n` times for array with `n` times
   As one switching operation can modify, two elements so we don't have to do it `n` times. In fact for even `n=2m`, `m` times loop is enough and for odd `n=2m+1` is `m` times loop is enough as well because the middle element does not have to be modified.
2. Updated elements is reassigned to the original index.
   In the bold parts above, we use the updated value as a value to switch. This happens because we cannot switch two elements simultaneously. 

## Fix the Program
Based on this analysis, we would fix this program.

1. For the first one we modify `i < arr.length` to `i < arr.length / 2`.
2. For the second one, we store the element in a new variable (`first`, `last`) and switch them respectively.

```java
static void reverseInPlace(int[] arr) {
  for (int i = 0; i < arr.length / 2; i += 1) {
    int first = arr[i];
    int last = arr[arr.length - i - 1];
    arr[i] = last;
    arr[arr.length - i - 1] = first;
  }
}
```

# How to Utilize `find` Command
In this section, we would explain how to use `find` command by giving 8 examples.
In the following example, we expect to have the following file strucutre.

```
some-files
  - empty
  - even-more-files
    - a.txt
    - d.java
  - more-files
    - b.txt
    - c.txt
  - a.txt
```
We can use bash command with options like `-[option name]`. To find out what options it have, we can use `man` command to consult the documentation. 
To find out options for `find`, we can use `man find`.

We have to specify the directly to search.
```
find .
```
This command returns all the files below the directly including `.` itself.
```
.
./empty
./even-more-files
./even-more-files/d.java
./even-more-files/a.txt
./more-files
./more-files/c.txt
./more-files/b.txt
./a.txt
```

We pick up 4 options to introduce.
## `-name` option

> True if the last component of the pathname being examined matches pattern.
> Special shell pattern matching characters (“[”, “]”, “*”, and “?”) may be used as part of pattern. 
> These characters may be matched explicitly by escaping them with a backslash (“\”).

We can use this option to specify the specific files or directories which mathes the pattern provided. 

For example, 

```
find . -name "*.txt"
```
```
./even-more-files/a.txt
./more-files/c.txt
./more-files/b.txt
./a.txt
```
This command find all files with `.txt` exntention and successfully found all text files.. Withtout `"`, this command can expand first to `a.txt` and then run `find . -name a.txt`.

```
find . -name "a*"
```
```
./even-more-files/a.txt
./a.txt
```
We can also find files with `a`

## `-depth` or `-d` option

> Always true; same as the non-portable -d option.
> Cause find to perform a depth-first traversal, i.e., directories are visited in post-order and all entries in a directory will be acted on before the directory itself. 
> By default, find visits directories in pre-order, i.e., before their contents.

We can specify how deep the command to search.

For example, 

```
find . -d 1
```
```
./empty
./even-more-files
./more-files
./a.txt
```
This command only listed files and directies in the current working directly.

We can use multiple options at the same time.
```
find . -depth 1 -name "*.txt"
```
```
./a.txt
```

## `-type` option

> True if the file is of the specified type.  Possible file types are as follows:
> * b       block special
> * c       character special
> * d       directory
> * f       regular file
> * l       symbolic link
> * p       FIFO
> * s       socket

We can specify the type of the file or directy to search.

For example, 

```
find . -type f
```
```
./even-more-files/d.java
./even-more-files/a.txt
./more-files/c.txt
./more-files/b.txt
./a.txt
```
This command listed only files excluding directries.
```
find . -type d
```
```
.
./empty
./even-more-files
./more-files

```
In this case, this command listed only directies exluding files.

## `-empty` option
> True if the current file or directory is empty.

With this option, find command list files with size 0 or directies without any files in.

We wrote the content all txt files but `d.java` does not have any content.

```
find . -empty
```
```
./empty
./even-more-files/d.java
```

List up `empty` because it does not have any file or directly in and `d.java` because its file size is 0.

We can use multiple options for this case as well.

```
find . -empty -type f -name "*.java"
```
```
./even-more-files/d.java
```

# Citation
When finding the use cases for find empty option, we used ChatGPT.

```
What is empty option of find.
```
(I used it in Japanse but I did it again in English.)

> The `-empty` option of the `find` command in Unix-like operating systems is used to match files and directories that are empty. An empty file has a size of 0 bytes, and an empty directory contains no entries (no other files or directories within it).
> Using the `-empty` option with `find` allows you to locate these empty files and directories within the filesystem. This can be particularly useful for cleaning up or managing files and directories in your system.
> Here's a basic example of how to use the `-empty` option with `find`:
```sh
find /path/to/search -empty
```
> This command will search through `/path/to/search` and all its subdirectories for empty files and directories, and list them.
> For instance, if you wanted to find all empty files and directories in the current directory and its subdirectories, you would use:
```sh
find . -empty
```
> In this command, `.` represents the current directory. The `-empty` option instructs `find` to only match files and directories that are empty.