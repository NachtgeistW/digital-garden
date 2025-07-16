[Advice to a novice programmer](https://blog.plover.com/prog/katara-advice.html)  

#建议

Katara is taking a Data Structures course this year. The most recent assignment gave her a lot of trouble, partly because it was silly and made no sense, but also because she does not yet know an effective process for writing programs, and the course does not attempt to teach her. On the day the last assignment was due I helped her fix the remaining bugs and get it submitted. This is the memo I wrote to her to memorialize of the important process issues that I thought of while we were working on it.

---

1. You lost a lot of time and energy dealing with issues like: Using `vim`; copying files back and forth with scp; losing the network connection; the college shared machine is slow and yucky.
    It's important to remove as much friction as possible from your basic process. Otherwise it's like trying to cook with dull knives and rusty pots, except worse because it interrupts your train of thought. **You can't do good work with bad tools.**
    When you start the next project, start it in VScode in the beginning. And maybe set aside an hour or two before you start in earnest, just to go through the VSCode tutorial and familiarize yourself with its basic features, without trying to do that at the same time you are actually thinking about your homework. This will pay off quickly.
2. It's tempting to cut corners when writing code. For example:
    1. It's tempting to use the first variable of function name you think of instead of taking a moment to think of a suggestive one. You had three classes in your project, all with very similar names. You might imagine that this doesn't matter, you can remember which is which. But remembering imposes a tiny cost every time you do it. These tiny costs seem insignificant. But they compound.
    2. It's tempting to use a short, abbreviated variable or method name instead of a longer more recognizable one because it's quicker to type. Any piece of code is read more often than it is written, so that is optimizing in the wrong place. You need to optimize for quick and easy reading, at the cost of slower and more careful writing, not the other way around.
    3. It's tempting to write a long complicated expression instead of two or three shorter ones where the intermediate results are stored in variables. But then every time you look at the long expression you have to pause for a moment to remember what is going on.
    4. It's tempting to repeat the same code over and over instead of taking the time to hide it behind an interface. For example your project was full of `array[d-1900]` all over. This minus-1900 thing should have been hidden inside one of the classes (I forget which). Any code outside that had to communicate with this class should have done so with full year numbers like 1926. That way, when you're not in that one class, you can ignore and forget about the issue entirely. Similarly, if code outside a class is doing the same thing in more than once place, it often means that the class needs another method that does that one thing. You add that method, and then the code outside can just call the method when it needs to do the thing. You advance the program by extending the number of operations it can perform without your thinking of them.
    5. If something is messy, it is tempting to imagine that it doesn't matter. It does matter. Those costs are small but compound. Invest in cleaning it up messy code, because if you don't the code will get worse and worse until the mess is a serious impediment. This is like what happens when you are cooking if you don't clean up as you go. At first it's only a tiny hindrance, but if you don't do it constantly you find yourself working in a mess, making mistakes, and losing and breaking things.
3. Debugging is methodical. Always have clear in your mind what question you are trying to answer, and what your plan is for investigating that question. The process looks like this:
    1. I don't like that it is printing out 0 instead of 1. Why is it doing that? Is the printing wrong, or is the printing correct but the data is wrong?
    2. I should go into the function that does the printing, and print out the data in the simplest way possible, to see if it is correct. (If it's already printing out the data in the simplest way possible, the problem must be in the data.)
    3. (Supposing that the it's the data that is bad) Where did the bad data come from? If it came from some other function, what function was it? Did that function make up the wrong data from scratch, or did it get it from somewhere else?
        If the function got the data from somewhere else, did it pass it along unchanged or did it modify the data? If it modified the data, was the data correct when the function got it, or was it already wrong?    
    The goal here is to point the Finger of Blame: What part of the code is really responsible for the problem? First you accuse the code that actually prints the wrong result. Then that code says “Nuh uh, it was like that when I got it, go blame that other guy that gave it to me.” Eventually you find the smoking gun.    
    Novice programmers often imagine that they can figure out what is wrong from looking at the final output and intuiting the solution Sherlock Holmes style. This is mistaken. _Nobody_ can do this. Debugging is an engineering discipline: You come up with a hypothesis, then test the hypothesis. Then you do it again.    
4. Ask Dad for assistance when appropriate. I promise not to do anything that would violate the honor code.    

---

Something we discussed that I forgot to include in the memo that we discussed is: After you fix something significant, or add significant new functionality, make a checkpoint copy of the entire source code. This can be as simple as simply copying it all into separate folder. That way, when you are fixing the _next_ thing, if you mess up and break everything, it's easy to get back to a known-good state. The computer is really clumsy to use for many tasks, but it's just great at keeping track of information, so exploit that when you can.

I think CS curricula should have a class that focuses specifically on these issues, on the matter of _how do you actually write software?_

But they never do.

【给新手程序员的建议】
- 使用方便的编程工具，如VSCode，减少无关的摩擦。熟悉工具的使用可以节省时间和精力。
- 编写代码时不要急于求成，要思考有意义的变量和函数名，编写可读性好的代码。这有助于代码的维护。
- 避免重复代码，适当抽象封装。重复的代码往往表示需要新的函数或方法。
- 调试要有系统，先明确问题，提出假设，然后通过打印日志、追踪数据流等方法验证，不要盲目猜测。
- 解决重要问题或添加新功能后，拷贝一份完整代码的快照，以便回退。
- 必要时请教别人，但不要违反学术诚信。
- CS课程应该教授如何系统地编写软件，这一实用技能常被忽略。
- 作者总体建议要投入时间思考，实践良好的编程习惯，这对编写和维护代码都很重要。