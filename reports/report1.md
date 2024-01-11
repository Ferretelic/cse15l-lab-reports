# Lab Report 1 Remote Access and FileSystem (Week 1) {#week-1-lab-report}
In this blog, I would explain basic commands  (`cd`, `ls`, `cat`) which are useful when navigating through files when working on projects.

## Setup
In the following examples, I would assume the directory stractures as below:

```
/home/
|-lectures/
  |- lecture1
    |- hello.java
    |- text.txt
  |- lecture2
    |- array.java
  |- syllabus.txt
```

In each example, I show 3 examples as followings:
* A command with no arguments.
* A command with a path to a directory as an argument.
* A command with a path to a file as an argument.

## `cd` command
This command means "change directory", which changes your working directory to the directory given in an argument.
``` zsh
cd [path of a directory you want to move to (optional)]
```
### A command with no arguments
**Setup**
* Working Directory: `/home/lectures`

**Input**
``` zsh
cd
```
**Output**
``` output
```
**Result**
* Result: successful (no error).
* Woking Directory: `/home/`

**Explanation**
Working directory is moved to `/home/` without an output. This indicates `cd ` command change working directory to `/home/` by default when an argument is not given.

### A command with a path to a directory as an argument
**Setup**
* Working Directory: `/home/lectures`

**Input**
``` zsh
cd ./lecture1
```
**Output**
``` zsh
```
**Result**
* Result: successful (no error).
* Woking Directory: `/home/lectures/lecture1`

**Explanation**
Working directory is moved to `/home/lectures/lecture1` without an output. `cd` can take a path of a directory as an arugment, and this path can be a relative or absolute path like (`/home/lectures/lecture1`, `./lecture1`, `./lecture2`)

### A command with a path to a file as an argument
**Setup**
* Working Directory: `/home/lectures`

**Input**
``` zsh
cd ./lecture1/hello.java
```
**Output**
``` zsh
cd: not a directory: ./lecture1/hello.java
```
**Result**
* Result: error occured.
* Woking Directory: `/home/lectures`

**Explanation**
Working directory is not changed, an error occured saying `not a directory`. As `cd` is a command to change the working directory, it cannot take file path as an argument, which is the reason why the error occured.

## `ls` command
This command means "list", which shows a list of files and directories in the working directory.
``` zsh
ls [path of a directory which you want to look at (optional)]
```
### A command with no arguments
**Setup**
* Working Directory: `/home/lectures`

**Input**
``` zsh
ls
```
**Output**
``` zsh
lecture1  lecture2  syllabus.txt
```
**Result**
* Result: successful (no error).
* Woking Directory: `/home/lectures`

**Explanation**
According to the strucuture of files presented above, `lectures` directory has two directories (`lecture1` and `lecture2`) and one file (`syllabus.txt`). Therefore, `ls` command shows three directory or file names.

### A command with a path to a directory as an argument
**Setup**
* Working Directory: `/home/lectures`

**Input**
``` zsh
ls ./lecture1
```
**Output**
``` zsh
hello.java  text.txt
```
**Result**
* Result: successful (no error).
* Woking Directory: `/home/lectures/lecture1`

**Explanation**
If `ls` command takes an argument of a path of a directry, it shows the list of files and directries in the given directory. Threfore with `ls ./lecture1`, it shows the list of files and diretories in the directory `./lecture1`, which are `hello.java` and `text.txt`.

### A command with a path to a file as an argument
**Setup**
* Working Directory: `/home/lectures`

**Input**
``` zsh
ls ./lecture1/hello.java
```
**Output**
``` zsh
./lecture1/hello.java
```

**Result**
* Result: successful (no error).
* Woking Directory: `/home/lectures`

**Explanation**
If `ls` command takes file path as an arugment, it returns the file path as given. If it takes a relative path, it returns the given relative path, and if it takes an absolute path, it returns the given absolute path.

## `cat` command
This command means "concatenate" and shows the content of a file which is assigned by an argument.

``` zsh
cat [A path of a file you want to look at (optional)]
```
### A command with no arguments
**Setup**
* Working Directory: `/home/lectures`

**Input**
``` zsh
cat
```
**Output**
``` zsh
```
**Result**
* Result: successful (no error).
* Woking Directory: `/home/lectures`

**Explanation**
Without arguments, `cat` command shows nothing but it doesn't cause error. As no file is assigned, so this command doesn't have anything to display, but it does not cause an error.

### A command with a path to a directory as an argument
**Setup**
* Working Directory: `/home/lectures`

**Input**
``` zsh
cat ./lecture1
```
**Output**
``` zsh
cat: ./lecture1: Is a directory
```
**Result**
* Result: error occured.
* Woking Directory: `/home/lectures/lecture1`

**Explanation**
`cat` is a command to show the content of a file, so it cannot take a path of a directory as an argument (directory doesn't have a content). Therefore, you need to provide a path of a file in an argument. 

### A command with a path to a file as an argument
**Setup**
* Working Directory: `/home/lectures`

**Input**
``` zsh
cat ./lecture1/hello.java
```
**Output**
``` zsh
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;

public class Hello {
  public static void main(String[] args) throws IOException {
    String content = Files.readString(Path.of(args[0]), StandardCharsets.UTF_8);    
    System.out.println(content);
  }
}
```

**Result**
* Result: successful (no error).
* Woking Directory: `/home/lectures`

**Explanation**
Given a path of a file `./lecture1/hello.java` as an arugment, this commands showed the content of the file (`hello.java`). This results shows , there is a java program to print out "Hello World".