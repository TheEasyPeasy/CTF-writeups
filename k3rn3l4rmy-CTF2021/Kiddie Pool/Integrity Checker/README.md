<h1>Integrity Checker</h1>
Points: 500

<h1>Category</h1>
Kiddie Pool/Reverse Engineering

<h1>Problem</h1>

![Alt text](https://i.imgur.com/cdF51EI.png "Title")

<h1>Solution</h1>

It's a JAR file so i decided to use my favourite bytecode editor - JByteMod (https://github.com/GraxCode/JByteMod-Beta) to solve this

First glance after upload jar into jbytemod show us 2 classes

![Alt text](https://i.imgur.com/TcywVyL.png "Title")

![Alt text](https://i.imgur.com/HQD2gnJ.png "Title")

![Alt text](https://i.imgur.com/qc4cHPB.png "Title")


```JAVA
import java.io.PrintStream;

public class Main {
    public Main() {
        Main throw1;
    }

    public static void main(String[] arrstring) throws Exception {
        try {
            String[] throw1;
            if (throw1.length != 1) {
                System.out.println("Please fill your password!");
                return;
            }
            if (throw1[0].length() != 5) {
                System.out.println("Password length is 5 characters!");
                return;
            }
            String throw2 = "\uadea\uad84\uaded\uadaf\uadd7\uad80\uad9f\uad84\uade0\uadf8\uadf3\uad91\uade3\uad9d\uadf3\uad83\uade2\uadf8\uadfb\uad97\uadff\uad87\uadf9\uad9a\uadef\uadfb\uadf3\uad8e\uade5\uad84\uad9f\uadb5";
            System.out.println("your flag: " + \u0000K3RN3L_CTF{{{{{{false true switch if true ^ null \u0000else if   if if false switch checkcast iadd goto fsub goto jump goto 0xFFFFF}}}}}}}{}{}{{{{}}}}{{}{}{}{}}{. ......//\\\\\\\\\\\\\\\\\\\\\\This is not joke!((String)"\uadea\uad84\uaded\uadaf\uadd7\uad80\uad9f\uad84\uade0\uadf8\uadf3\uad91\uade3\uad9d\uadf3\uad83\uade2\uadf8\uadfb\uad97\uadff\uad87\uadf9\uad9a\uadef\uadfb\uadf3\uad8e\uade5\uad84\uad9f\uadb5", (String)throw1[0]));
            return;
        }
        catch (Throwable throwable) {
            throw null;
        }
    }
}
```

```JAVA
import java.io.PrintStream;

/*
 * Illegal identifiers - consider using --renameillegalidents true
 */
public class \u0000K3RN3L_CTF{{{{{{false true switch if true ^ null \u0000else if   if if false switch checkcast iadd goto fsub goto jump goto 0xFFFFF}}}}}}}{}{}{{{{}}}}{{}{}{}{}}{ {
    public \u0000K3RN3L_CTF{{{{{{false true switch if true ^ null \u0000else if   if if false switch checkcast iadd goto fsub goto jump goto 0xFFFFF}}}}}}}{}{}{{{{}}}}{{}{}{}{}}{() {
        \u0000K3RN3L_CTF{{{{{{false true switch if true ^ null \u0000else if   if if false switch checkcast iadd goto fsub goto jump goto 0xFFFFF}}}}}}}{}{}{{{{}}}}{{}{}{}{}}{ throw1;
    }

    public static String  ......//\\\\\\\\\\\\\\\\\\\\\\This is not joke!(String string, String string2) throws Exception {
        try {
            void throw5;
            String throw4;
            StackTraceElement[] throw6 = new Throwable().getStackTrace();
            String throw7 = throw6[0].getMethodName();
            String throw8 = throw6[0].getClassName();
            char[] throw9 = throw8.toCharArray();
            char[] throw10 = throw5.toCharArray();
            if ((char)(throw9[0] ^ 3) != throw10[0] && (char)(throw9[1] ^ 12) != throw10[1] && (char)(throw9[2] ^ 32) != throw10[2] && (char)(throw9[3] ^ 5) != throw10[3] && (char)(throw9[4] ^ 67) != throw10[4]) {
                System.out.println("Your password input is invalid!");
                return "INVALID PASSWORD!";
            }
            int throw11 = throw7.hashCode();
            int throw12 = throw8.hashCode();
            int throw13 = throw6[1].getClassName().hashCode();
            char[] throw14 = throw4.toCharArray();
            for (int throw1 = 0; throw1 < throw14.length; ++throw1) {
                throw14[throw1] = (char)(throw14[throw1] ^ throw12 ^ throw11 ^ throw13);
            }
            for (int throw2 = 0; throw2 < throw14.length; throw2 += 2) {
                throw14[throw2] = (char)(throw14[throw2] ^ throw7.toCharArray()[0] ^ throw8.toCharArray()[0]);
            }
            for (int throw3 = 0; throw3 < 3; ++throw3) {
                throw14[throw3] = (char)(throw14[throw3] ^ 32);
            }
            return new String(throw14);
        }
        catch (Throwable throwable) {
            throw null;
        }
    }
}
```

The name of the task suggests that we will meet some <b>Integrity Check</b> inside, and it happened.
Let's look at garbage class
```JAVA
 StackTraceElement[] throw6 = new Throwable().getStackTrace();
            String throw7 = throw6[0].getMethodName(); //current MethodName
            String throw8 = throw6[0].getClassName();  //current ClassName
            char[] throw9 = throw8.toCharArray();
            char[] throw10 = throw5.toCharArray();
```

Variable <b>throw6</b> represents an array of stack trace elements, each representing one stack frame. The zeroth element of the array (assuming the array's length is non-zero) represents the top of the stack, which is the last method invocation in the sequence.<br />
So finally we have answer about IntegrityChecker that checks current method name and class name where's called (our garbage class name and method) <br />
Description says: <b>unfortunately class is garbage name, could you recover it</b> so we have to get original class name, also hint says we can find it in Java Class Structure <br />

https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-4.html#jvms-4.7.10 - The string referenced by the sourcefile_index item will be interpreted as indicating the name of the source file from which this <b>class file was compiled.</b>
 
 Also JByteMod can show us SourceFile when we choose class and switch to INFO
![Alt text](https://i.imgur.com/hpV0i9a.png "Title") <br />
<br />

Original class name while compiling JAR was **Decryptor**; Let's change it <br />
![Alt text](https://i.imgur.com/8BAzeB9.png "Title")

<b>Warning: References will not be updated!</b> <br/> So we have to change call reference in Main class, because class name changed but Main class is still calling garbage class name which do not exists anymore

Main class calls garbage class function, so we need to change **INVOKESTATIC** opcode to call <b>Decryptor</b>
![Alt text](https://i.imgur.com/2QeJ3pj.png "Title")

After change:
![Alt text](https://i.imgur.com/5PRgc0i.png "Title")

So for now we have almost like original JAR file that should pass our integrity check and decrypt flag. Our issue is the way we should go through, looking at decryption function

```JAVA

    public static String  ......//\\\\\\\\\\\\\\\\\\\\\\This is not joke!(String string, String string2) throws Exception {
        try {
            void throw5;
            String throw4;
            StackTraceElement[] throw6 = new Throwable().getStackTrace();
            String throw7 = throw6[0].getMethodName();
            String throw8 = throw6[0].getClassName();
            char[] throw9 = throw8.toCharArray();
            char[] throw10 = throw5.toCharArray();
            if ((char)(throw9[0] ^ 3) != throw10[0] && (char)(throw9[1] ^ 12) != throw10[1] && (char)(throw9[2] ^ 32) != throw10[2] && (char)(throw9[3] ^ 5) != throw10[3] && (char)(throw9[4] ^ 67) != throw10[4]) {
                System.out.println("Your password input is invalid!");
                return "INVALID PASSWORD!";
            }
            int throw11 = throw7.hashCode();
            int throw12 = throw8.hashCode();
            int throw13 = throw6[1].getClassName().hashCode();
            char[] throw14 = throw4.toCharArray();
            for (int throw1 = 0; throw1 < throw14.length; ++throw1) {
                throw14[throw1] = (char)(throw14[throw1] ^ throw12 ^ throw11 ^ throw13);
            }
            for (int throw2 = 0; throw2 < throw14.length; throw2 += 2) {
                throw14[throw2] = (char)(throw14[throw2] ^ throw7.toCharArray()[0] ^ throw8.toCharArray()[0]);
            }
            for (int throw3 = 0; throw3 < 3; ++throw3) {
                throw14[throw3] = (char)(throw14[throw3] ^ 32);
            }
            return new String(throw14);
        }
        catch (Throwable throwable) {
            throw null;
        }
    }
```

<br/>

We have two choices: <br/>
a) We have there password checks with some XOR so we can reverse it, get password and pass as parameter to program and look what happens<br/>
b) Get encrypted flag string from stack and try to decrypt it through this function<br/>

Let's check both of them!

<b>choice a)</b>

```JAVA
if ((char)(throw9[0] ^ 3) != throw10[0] && (char)(throw9[1] ^ 12) != throw10[1] && (char)(throw9[2] ^ 32) != throw10[2] && (char)(throw9[3] ^ 5) != throw10[3] && (char)(throw9[4] ^ 67) != throw10[4]) {
                System.out.println("Your password input is invalid!");
                return "INVALID PASSWORD!";
            }
```


throw9 = throw8.toCharArray() = Current class name (Decryptor) <br />
throw10 = second argument = input password <br />

```java
D ^ 3 == password[0]  == G
e ^ 12 == password[1] == i
c ^ 32 == password[2] == C
r ^ 5 == password[3]  == W
y ^ 67 == password[4] == :
p
t
o
r
```

This is probably our password to JAR. It's doesn't make sense but from XOR we got it, they are printable characters so everything seems good! :D

Let's save our changes and enable jar
![Alt text](https://i.imgur.com/VmaSTJp.png "Title")

![Alt text](https://i.imgur.com/8cA1uVq.png "Title")


We got it, call integrity check has been bypassed and we got flag
your flag: <b>flag{H3LL0_YOU_KN0W_SOURC3_FIL3}</b>
<br/>


<b>choice b)</b>

I copied Decryptor function and created simple java program, addictionaly renamed some variables and changed their return values

```JAVA
    public static void main(String[] args) {
        final String encryptedFlag = "\uadea\uad84\uaded\uadaf\uadd7\uad80\uad9f\uad84\uade0\uadf8\uadf3\uad91\uade3\uad9d\uadf3\uad83\uade2\uadf8\uadfb\uad97\uadff\uad87\uadf9\uad9a\uadef\uadfb\uadf3\uad8e\uade5\uad84\uad9f\uadb5";

        final String decryptorMethodName = " ......//\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\This is not joke!";
        int decryptorMethodNameHash = decryptorMethodName.hashCode();
        int throw12 = "Decryptor".hashCode();
        int throw13 = "Main".hashCode();
        char[] throw14 = encryptedFlag.toCharArray();

        int throw3;
        for(throw3 = 0; throw3 < throw14.length; ++throw3) {
            throw14[throw3] = (char)(throw14[throw3] ^ throw12 ^ decryptorMethodNameHash ^ throw13);
        }

        for(throw3 = 0; throw3 < throw14.length; throw3 += 2) {
            throw14[throw3] = (char)(throw14[throw3] ^ decryptorMethodName.toCharArray()[0] ^ "Decryptor".toCharArray()[0]);
        }

        for(throw3 = 0; throw3 < 3; ++throw3) {
            throw14[throw3] = (char)(throw14[throw3] ^ 32);
        }
        System.out.println(throw14);
    }
```
output: <b>flag{H3LL0_YOU_KN0W_SOURC3_FIL3}</b>


