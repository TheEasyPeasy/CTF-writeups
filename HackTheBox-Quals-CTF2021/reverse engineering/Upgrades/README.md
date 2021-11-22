<h1>Upgrades</h1>

<h1>Category</h1>
Reverse Engineering

<h1>Problem</h1>

![Alt text](https://cdn.discordapp.com/attachments/909755224837730334/911325588973813780/unknown.png "Title")

<h1>Solution</h1>

We got .pptm file (Microsoft Presentation) so i thought about find any macros inside. We are going to use oletools:

```JAVA
root@vps:~/ctf# olevba -c ./Upgrades.pptm
olevba 0.60 on Python 2.7.17 - http://decalage.info/python/oletools
===============================================================================
FILE: ./Upgrades.pptm
Type: OpenXML
WARNING  For now, VBA stomping cannot be detected for files in memory
-------------------------------------------------------------------------------
VBA MACRO Module1.bas
in file: ppt/vbaProject.bin - OLE stream: u'VBA/Module1'
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Private Function q(g) As String
q = ""
For Each I In g
q = q & Chr((I * 59 - 54) And 255)
Next I
End Function
Sub OnSlideShowPageChange()
j = Array(q(Array(245, 46, 46, 162, 245, 162, 254, 250, 33, 185, 33)), _
q(Array(215, 120, 237, 94, 33, 162, 241, 107, 33, 20, 81, 198, 162, 219, 159, 172, 94, 33, 172, 94)), _
q(Array(245, 46, 46, 162, 89, 159, 120, 33, 162, 254, 63, 206, 63)), _
q(Array(89, 159, 120, 33, 162, 11, 198, 237, 46, 33, 107)), _
q(Array(232, 33, 94, 94, 33, 120, 162, 254, 237, 94, 198, 33)))
g = Int((UBound(j) + 1) * Rnd)
With ActivePresentation.Slides(2).Shapes(2).TextFrame
.TextRange.Text = j(g)
End With
If StrComp(Environ$(q(Array(81, 107, 33, 120, 172, 85, 185, 33))), q(Array(154, 254, 232, 3, 171, 171, 16, 29, 111, 228, 232, 245, 111, 89, 158, 219, 24, 210, 111, 171, 172, 219, 210, 46, 197, 76, 167, 233)), vbBinaryCompare) = 0 Then
VBA.CreateObject(q(Array(215, 11, 59, 120, 237, 146, 94, 236, 11, 250, 33, 198, 198))).Run (q(Array(59, 185, 46, 236, 33, 42, 33, 162, 223, 219, 162, 107, 250, 81, 94, 46, 159, 55, 172, 162, 223, 11)))
End If
End Sub


-------------------------------------------------------------------------------
VBA MACRO Slide1.cls
in file: ppt/vbaProject.bin - OLE stream: u'VBA/Slide1'
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Private Sub Label1_Click()

End Sub

/usr/local/lib/python2.7/dist-packages/msoffcrypto/method/ecma376_agile.py:8: CryptographyDeprecationWarning: Python 2 is no longer supported by the Python core team. Support for it is now deprecated in cryptography, and will be removed in the next release.
  from cryptography.hazmat.backends import default_backend

```

At first glance we see some char arrays, and function "q" what's probably decrypt function. I decided to re-write functionality into java

VBA:
```java
Private Function q(g) As String
q = ""
For Each I In g
q = q & Chr((I * 59 - 54) And 255)
Next I
End Function
```

Java:
```java
    public static String decrypt(final char[] array) {
        for (int i=0; i<array.length; i++) {
            array[i] = (char) ((array[i] * 59 - 54) & 255);
        }
        return new String(array);
    }
```

Next i copied all arrays, decrypt and print out:

```java
    public static void main(String[] args) throws Exception {
        System.out.println(decrypt(new char[] {245, 46, 46, 162, 245, 162, 254, 250, 33, 185, 33}));
        System.out.println(decrypt(new char[] {215, 120, 237, 94, 33, 162, 241, 107, 33, 20, 81, 198, 162, 219, 159, 172, 94, 33, 172, 94}));
        System.out.println(decrypt(new char[] {245, 46, 46, 162, 89, 159, 120, 33, 162, 254, 63, 206, 63}));
        System.out.println(decrypt(new char[] {89, 159, 120, 33, 162, 11, 198, 237, 46, 33, 107}));
        System.out.println(decrypt(new char[] {232, 33, 94, 94, 33, 120, 162, 254, 237, 94, 198, 33}));
        System.out.println(decrypt(new char[] {81, 107, 33, 120, 172, 85, 185, 33 }));
        System.out.println(decrypt(new char[] {154, 254, 232, 3, 171, 171, 16, 29, 111, 228, 232, 245, 111, 89, 158, 219, 24, 210, 111, 171, 172, 219, 210, 46, 197, 76, 167, 233 }));
        System.out.println(decrypt(new char[] {215, 11, 59, 120, 237, 146, 94, 236, 11, 250, 33, 198, 198 }));
        System.out.println(decrypt(new char[] {59, 185, 46, 236, 33, 42, 33, 162, 223, 219, 162, 107, 250, 81, 94, 46, 159, 55, 172, 162, 223, 11 }));
    }
```

After launch we got strings:

```
Add A Theme
Write Useful Content
Add More TODO
More Slides
Better Title
username
HTB{33zy_VBA_M4CR0_3nC0d1NG}
WScript.Shell
cmd.exe /C shutdown /S
```

our flag: <b>HTB{33zy_VBA_M4CR0_3nC0d1NG}</b>

