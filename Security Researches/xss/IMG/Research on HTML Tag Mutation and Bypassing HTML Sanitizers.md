
I recently came across an intriguing study by liveoverflow, a renowned cybersecurity researcher, that sheds light on an interesting behavior of HTML tags. In his videos, liveoverflow demonstrates how HTML tags can be mutated and manipulated to potentially bypass HTML sanitizers and execute malicious code.

The first observation made was the unexpected behavior of HTML when a tag is created from a number. It was discovered that the browser renders the number as a valid HTML tag, which led to further investigation.

By adding another tag inside this attribute, it was found that the browser executed the HTML accordingly. This led to the exploration of injecting XSS payloads within tags. It was discovered that tags like `<h1>` could survive the mutation, and the browser rendered them as expected.

To test the limitations, an attempt was made to inject an XSS payload into the `<img>` tag. Surprisingly, the payload executed successfully, indicating a potential vulnerability.

![IMG: Pasted image 20230714134257.png]

Further experimentation involved changing the tag from `22` to `div`, which resulted in normal HTML execution without any mutation. This demonstrated the potential bypass of HTML sanitizers by manipulating tags.

![IMG: Pasted image 20230714134608.png]

However, it is worth noting that HTML sanitization primarily occurs on the client-side, which may limit the effectiveness of bypassing sanitizers.

In the documentation of parse errors in HTML, it was revealed that starting tags must be ASCII-alpha characters [a-z | A-Z]. Numeric tags are not allowed as starting tags, but they can be added after the initial character. This further confirmed the limitations and expectations of HTML parsing.

![IMG: Pasted image 20230714141451.png]

To summarize, the research by liveoverflow highlights the possibility of HTML tag mutation and the potential bypass of HTML sanitizers. However, it is important to consider the context of client-side sanitization and the limitations imposed by HTML parsing rules.

I encourage you to explore liveoverflow's videos ([Video 1](https://www.youtube.com/watch?v=HUtkW2gjC8Q) and [Video 2](https://www.youtube.com/watch?v=lG7U3fuNw3A)) <22>test</22>for a detailed understanding of this intriguing research. Additionally, platforms such as grep.app and debuggex.com can be valuable resources for further exploration in this domain.