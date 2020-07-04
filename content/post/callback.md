---
title: Call me back
date: 2017-12-02 22:09:50
tags:
---
Explore the simple yet powerful world of callbacks<!--more--> with a couple of examples in java . Imagine it's a pleasant sunday and you have decided to bake a chocolate cake. Halfway into the process you realise that you are missing a key ingredient, cocoa. Now there are two ways to proceed further, one you yourself can walk down to the nearest store and get a hold of cocoa or you can ask a person to do it for you and carry on the baking process whilst he is away.

Now considering that you are hungry and you want to eat the cake as soon as possible the second option seems like the best choice. So you handover some money to that person, ask him/her to buy the cocoa. While this process is being carried out you are not waiting and continue with the baking. After he arrives with the cocoa you take it from him complete the cake.

Now in the world of software and programming, people are making the cake all the time. The process cannot wait for something to complete in order to proceed further. This scenario is achieved through callbacks. So the usual method is - you register a callback function for a particular task, initiate that task and finally receive the result inside the callback function you registered previously. Let's look at an example.

{{< codeblock "callback.java" "java"  >}}
void makeACake() {
    getIngredients();
    getCocoa(new CakeCallback() {

        @Override
        public void onCocoaReceived(String result) {
            System.out.println(result);
            prepareTheDough();
            mixAll();
            bakeInTheOven();
        }            
    });
    
} 
{{< /codeblock >}}

So you gathered all the ingredients and initiated a process for obtaining cocoa. While that was being executed you were not idle and made the dough. Your getCocoa function might look something like this.

{{< codeblock "callback.java" "java"  >}}
void getCocoa(CakeCallback callback) {
    takeMoney();
    goToStore();
    callback.onCocoaReceived("Yay!! got cocoa");
}
{{< /codeblock >}}

The CakeCallback in this case is a simple java interface that contains one method for delivering the cocoa.

{{< codeblock "callback.java" "java"  >}}
public interface CakeCallback {
    void onCocoaReceived(String result);
}
{{< /codeblock >}}

The android framework uses callbacks all the time. The View.OnClickListener is an example of a simple callback mechanism. When you set an onClick listener for a view component android takes a callback as an input argument. Whenever that component is cliked the listener is fired and you receive a callback inside the onClick method. Any operation that must not block the current execution can do so by using a callback.