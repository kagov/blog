---
title: Name entity recognition using CoreNLP
date: 2018-12-30 17:37:39
tags: 
  - nlp
---

Chatbots are a great way to interact with customers and get things done at a really quick pace. <!--more-->Instead of clicking buttons, filling out forms and scrolling endlessly a chatbot can act like a companion, amuse the user with trivia, while delivering value at the same time in a more conversational and human way. 
One of the key tasks in working of a chatbot is the Name Entity Recognition (NER). Wiki says a named entity is similar to a real world object like a person, location, food items etc. Below you can see a few examples for named entities.

{{< rawhtml >}}
<br/>
{{< /rawhtml >}}

{{< gist kagov 60d6fc30f348832d0e45639eb3af6469 >}}


{{< rawhtml >}}
<br/>
{{< /rawhtml >}}

[CoreNLP](https://stanfordnlp.github.io/CoreNLP/) supports a few types of entites like person, time, date etc. Here is the list of all the named entities supported by CoreNLP. The other entities mentioned above are custom entites and they have to be extracted seperately. In order to achieve this we are going to use CoreNLP's RegexNER. 
As described in their website RegexNER is a rule based interface for doing custom entity recognition. A simple text file that contains all the details of the entites must be provided during the time of extraction and the pipeline takes care of annotating the token with the corresponding NER. A sample file might look like this.

{{< rawhtml >}}
<br/>
{{< /rawhtml >}}
{{< gist kagov 04627fdeec7aa87302c980f22a2986b1 >}}
{{< rawhtml >}}
<br/>
{{< /rawhtml >}}
Note that they are tabs and not spaces. The formatting of the file is very important for extraction and if improper the extraction fails and an exception is thrown. Let's dig in.
{{< rawhtml >}}
<br/>
{{< /rawhtml >}}
{{< codeblock "pom.xml" "xml"  >}}
<dependency>
  <groupId>edu.stanford.nlp</groupId>
  <artifactId>stanford-corenlp</artifactId>
  <version>3.9.1</version>
</dependency>
// models
<dependency>
  <groupId>edu.stanford.nlp</groupId>
  <artifactId>stanford-corenlp</artifactId>
  <version>3.9.1</version>
  <classifier>models</classifier>
</dependency>
{{< /codeblock >}}

{{< rawhtml >}}
<br/>
{{< /rawhtml >}}

Before setting up the extraction pipeline we will set the properties that will include our custom entity file.

{{< rawhtml >}}
<br/>
{{< /rawhtml >}}


{{< gist kagov 1ae08af5666b2bc14993ab143abb434c >}}

{{< rawhtml >}}
<br/>
{{< /rawhtml >}}

The fileName is the path of the custom entity file. You can provide it from anywhere like the file system, url or classpath also. Here I will include the entities.txt file in the resources folder of the project and the library will pick it up from there. Let us build the pipeline now.

{{< rawhtml >}}
<br/>
{{< /rawhtml >}}


{{< gist kagov 3f00b0728e2d7f3e6b3e1bb7dbdbeb6c >}}

{{< rawhtml >}}
<br/>
{{< /rawhtml >}}

We have built an annotation pipeline, provided the necessary properties including our entity file. The dateFormat is nothing but an instance of SimpleDateFromat with a date pattern.
The annotation contains all the annotated data but we cannot see it till now. So let's extract the output.

{{< rawhtml >}}
<br/>
{{< /rawhtml >}}

Now usually if you look for blogs related to corenlp everybody extracts a CoreMap instance from the annotation and iterate through each token and construct the output but I find that a little cumbersome and hence came up with a way to avoid it.

{{< rawhtml >}}
<br/>
{{< /rawhtml >}}

{{< codeblock "NerExtractor.java" "java"  >}}
new JSONOutputter().print(annotation,System.out);
{{< /codeblock >}}

{{< rawhtml >}}
<br/>
{{< /rawhtml >}}

The above line of code does a few things in the hindsight. It extracts the annotated data from the annotation, constructs a JSON and writes that to the output stream that is provided to it.
 For the sake of convenience I am just going to print it out on the console. You can feel free to provide any other stream such as a file or even an HTTP response stream. (Just set the content type header to application/json if using the HTTP servlet response stream).

{{< rawhtml >}}
<br/>
{{< /rawhtml >}}

Once you run this code you can find out a neat JSON printed on your console and the ner field for will contain the value that you specified in the file. 
The complete source code can be found on [github](https://github.com/kagov/extractner).

