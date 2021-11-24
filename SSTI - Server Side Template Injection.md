Server Side Template Injection

https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection#jinja2-python

# What is server-side template injection?

A server-side template injection occurs when an attacker is able to use native template syntax to inject a malicious payload into a template, which is then executed server-side.

**Template engines** are designed to **generate web** pages by **combining** **fixed** templates with **volatile** data. Server-side template injection attacks can occur when **user input** is concatenated directly **into a template**, rather than passed in as data. This allows attackers to **inject arbitrary template directives** in order to manipulate the template engine, often enabling them to take **complete control of the server**.

An example of vulnerable code see the following one:

````bash
$output = $twig->render("Dear " . $_GET['name']);
````

In the previous example **part of the template** itself is being **dynamically generated** using the `GET` parameter `name`. As template syntax is evaluated server-side, this potentially allows an attacker to place a server-side template injection payload inside the `name` parameter as follows:

````bash
http://vulnerable-website.com/?name={{bad-stuff-here}}
````

# Constructing a server-side template injection attack

![[Pasted image 20211124114844.png]]

## Detect

As with any vulnerability, the first step towards exploitation is being able to find it. Perhaps the simplest initial approach is to try **fuzzing the template** by injecting a sequence of special characters commonly used in template expressions, such as the polyglot `${{<%[%'"}}%\`. In order to check if the server is vulnerable you should **spot the differences** between the response with **regular data** on the parameter and the **given payload**. If an **error is thrown** it will be quiet easy to figure out that **the server is vulnerable** and even which **engine is running**. But you could also find a vulnerable server if you were **expecting** it to **reflect** the given payload and it is **not being reflected** or if there are some **missing chars** in the response.

### Detect - Plaintext context

The given input is being **rendered and reflected** into the response. This is easily **mistaken for a simple** [**XSS**](/pentesting-web/xss-cross-site-scripting) vulnerability, but it's easy to differentiate if you try to set **mathematical operations** within a template expression:

```bash
{{7*7}}
${7*7}
<%= 7*7 %>
${{7*7}}
#{7*7}
```

## Identify

Once you have detected the template injection potential, the next step is to identify the template engine. Although there are a huge number of templating languages, many of them use very similar syntax that is specifically chosen not to clash with HTML characters.

If you are lucky the server will be **printing the errors** and you will be able to find the **engine** used **inside** the errors. Some possible payloads that may cause errors:

![[Pasted image 20211124115052.png]]

Otherwise, you'll need to manually **test different language-specific payloads** and study how they are interpreted by the template engine. A common way of doing this is to inject arbitrary mathematical operations using syntax from different template engines. You can then observe whether they are successfully evaluated. To help with this process, you can use a decision tree similar to the following:

![](https://book.hacktricks.xyz/~/files/v0/b/gitbook-28427.appspot.com/o/assets%2F-L_2uGJGU7AVNRcqRvEi%2F-M7O4Hp6bOFFkge_yq4G%2F-M7OCvxwZCiaP8Whx2fi%2Fimage.png?alt=media&token=4b40cf58-5561-4925-bc86-1d4689ca53d1)

# Exploit
https://github.com/epinna/tplmap - Tool that helps doing code injection and SSTI