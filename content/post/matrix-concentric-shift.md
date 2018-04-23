+++
date = "2016-10-06T19:46:09+03:00"
title = "matrix concentric shift"
categories = ["programming kata"]
+++

<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#orgheadline1">1. Problem</a></li>
<li><a href="#orgheadline2">2. Analysis</a></li>
<li><a href="#orgheadline3">3. Source Code</a></li>
<li><a href="#orgheadline4">4. Build script</a></li>
<li><a href="#orgheadline5">5. How to run</a></li>
</ul>
</div>
</div>

I suck at algorithms, even though I have a Machine Learning and Algorithms master degree.
It makes me frown everytime in coding interviews.
So I decided to practice more often, hope that I can get better at it.
At least not afraid of it.
Now this is the first one.

# Problem<a id="orgheadline1"></a>

Rotate a square matrix clockwise concentrically by 1. For example:

    1 2 3
    4 5 6
    7 8 9

will become

    4 1 2
    7 5 3
    8 9 6

# Analysis<a id="orgheadline2"></a>

Ok, the problem in question is quite easy to understand, but instinct tells me that there maybe multiple ways to solve, but maybe no one will be substaintially easier than the rest.
So where to start? Let's start by thinking out loud. Let's assume we use java here.

First, two dimension array is probably most familiar to people as for presenting matrix. Um.. I couldn't think of any other options yet so let's use that for now.
Well, if the data is stored as two dimension array, then the rotation seems to be a bit hard to manipulate, since it is by 1 slot instead of, for example by 90 degree.
And the rotation is concentric, feels it's gonna be a whole lot of index manipulations. *sigh*, I hate complex index games like this. But let's see how hard can it be.

*10 minutes passed while staring at the paper&#x2026;*

That's enough staring to give me the conviction that I can't solve it that easily. So, maybe some drawing and doodling could help? Let's try. Having one thing in my mind, which is 
trying to find some rules related to indexes, I came up with the following doodle:

{{< figure src="/img/matrix-rotation-1.jpg" >}}

If we can find for each position, where the new value will come from, then we can create a new matrix and pick the correctly value from the old matrix.
Not hard to see that there are 5 categories, which are:
(1) get the new value from left 
(2) get the new value from right 
(3) get the new value from top 
(4) get the new value from below 
(5) new value is itself

Now the problem is to find out how to make this classification through code. After some thinking, I came up with the next doodle:

{{< figure src="/img/matrix-rotation-2.jpg" >}}

The two diagonal lines seem to put the elements into the correct category perfectly, but why is this, what is so special about the diagonal lines? Well, if you have some basic math
knowledge, it will occur to you that the green line means something like: `i = j`, and the yellow line means something like: `i + j = n`. So now it is very clear that each line divides 
the matrix into 2 parts. The yellow line: `i + j > n` and `i + j < n`, (n is the dimension of the matrix), and the green line `i > j` and `i < j`. Em, seems we are very close now.
Each category is the intersection of two of the 4 parts. If we can translate that into some if and else clauses, I guess we have found a solution!

{{< figure src="/img/matrix-rotation-3.jpg" >}}

# Source Code<a id="orgheadline3"></a>

{{< highlight java >}}
    package com.coding.challenges.concentricmatrix;
    
    import java.util.*;
    
    class Main
    {
        private static int[] convertStrArr2IntArr(int len, String[] arr) {
            int[] result = new int[len];
            for (int i = 0; i < arr.length; i++) {
                result[i] = Integer.parseInt(arr[i]);
            }
            return result;
        }
    
        private static void readInLines(int lineNum, int[][] data, Scanner scanner) {
            String line = scanner.nextLine();
            String[] lSplit = line.split(" ");
            int colNum = lSplit.length;
            data[lineNum] = convertStrArr2IntArr(colNum, lSplit);
        }
        public static void main(String [] args) throws Exception
        {
            Scanner sc = new Scanner(System.in);
            int  numberOfLines = Integer.parseInt(sc.nextLine());
            int[][] data = new int[numberOfLines][];
            for(int t = 0; t < numberOfLines; t++) {
                readInLines(t, data, sc);
            }
            if (!validateSqr(data)) {
                System.out.print("ERROR");
                return;
            }
    
            printResult(rotate(numberOfLines, data));
        }
    
        private static int[][] rotate(int n, int[][] data) {
            int[][] result = new int[n][n];
            for (int i = 0; i < result.length; i++) {
                for (int j = 0; j < result[i].length; j++) {
                    fill(n, result, data, i, j);
                }
            }
            return result;
        }
    
        private static void fill(int n, int[][] result, int[][] data, int i, int j) {
            if (i > j) {
                if (i + j < n - 1) {
                    result[i][j] = data[i + 1][j];
                } else {
                    result[i][j] = data[i][j + 1];
                }
            } else if (i < j) {
                if (i + j <= n - 1) {
                    result[i][j] = data[i][j - 1];
                } else {
                    result[i][j] = data[i - 1][j];
                }
            } else {
                if (i * 2 < n - 1) {
                    result[i][j] = data[i + 1][j];
                } else if (i * 2 > n - 1) {
                    result[i][j] = data[i - 1][j];
                } else {
                    result[i][j] = data[i][j];
                }
            }
        }
    
        private static boolean validateSqr(int[][] data) {
            int xLen = data.length;
            for (int[] aData : data) {
                int yLen = aData.length;
                if (yLen != xLen) {
                    return false;
                }
            }
            return true;
        }
    
        private static void printResult(int[][] data) {
            for (int[] aData : data) {
                for (int anAData : aData) {
                    System.out.print(anAData + " ");
                }
                System.out.println();
            }
        }
    }

{{< /highlight >}}

# Build script<a id="orgheadline4"></a>

{{< highlight groovy >}}

    repositories {
        mavenCentral()
    }
    
    apply plugin: "java"
    
    sourceSets {
        main.java.srcDir "src"
    }
    
    jar {
        manifest.attributes "Main-Class": "com.coding.challenges.concentricmatrix.Main"
    }

{{< /highlight >}}

# How to run<a id="orgheadline5"></a>

Press `C-c C-c` on the code block below.

{{< highlight groovy >}}

    ;; tangle the source code
    (org-babel-tangle)
    ;; export to pdf
    (org-latex-export-to-pdf)
    ;; build
    (shell-command-to-string "gradle jar")

{{< /highlight >}}
