# Building Front-End Web Apps with Plain JavaScript

[Original URL](https://oxygen.informatik.tu-cottbus.de/webeng/JsFrontendApp/book/)

> Warning: This tutorial book is a draft version, so it may still contain errors and may still be incomplete in certain respects. Please report any issue to G.Wagner@b-tu.de.This tutorial book is...

Warning: This tutorial book is a draft version, so it may still contain errors and may still be incomplete in certain respects. Please report any issue to G.Wagner@b-tu.de.

This tutorial book is available as an open access online book (HTML) and as an e-book (PDF) via its [book webpage](http://web-engineering.info/JsFrontendApp-Book).

Copyright © 2014-2015 Gerd Wagner

[]() This book, along with any associated source code, is licensed under [The Code Project Open License (CPOL)](http://www.codeproject.com/info/cpol10.aspx), implying that the associated code is provided "as-is", can be modified to create derivative works, can be redistributed, and can be used in commercial applications, but the book must not be distributed or republished without the author's consent.

**Revision History** 20151112 gw Replace the <<stdid>> property stereotype by {id} property modifiers in class diagrams. Add sample data tables to each tutorial chapter. Add more practice projects with design models and sample data tables. Add quiz questions. 20150701 gw Add section about CSS to the "Foundations" chapter, detach the "More on JavaScript" chapter from the "Foundations" chapter. 20150520 gw Add chapter about enumerations. 20140924 gw Incorporate feedback by Felix Lehmann. 20140221 gw Incorporate feedback by Sorin Carbunaru. 20140212 gw Create first version.

Thanks to Sorin Carbunaru and Felix Lehmann for proof-reading early versions of parts of this tutorial book.

--------------------------------------------------------------------------------

This tutorial book shows how to build front-end web applications with plain JavaScript, not using any (third-party) framework or library. It follows a "learning by doing" approach, which means that you don't have to read lots of text about the intricacies of JavaScript (like in many JavaScript programming books), but rather you just focus on the essential parts of JavaScript and only read the minimum needed for starting to code your first app. By learning from the examples provided in the book, you can quickly improve your understanding of basic concepts and techniques, and by downloading the complete code of these example apps and using it as a starting point for your own projects, you learn best practices and experience the joy of building something that really works.

## []()1\. Why is JavaScript a Good Choice for Building Web Apps?

Today, JavaScript is not just a programming language, but rather a platform, which offers many advantages over other platforms:

1. It's the only language that enjoys native support in web browsers.

2. It's the only language that allows building web apps with just one programming languge. All other languages (like Java and C#) can only be used on the back-end and need to be combined with front-end JavaScript, so developers need to master at least two programming languages.

3. It's the only language that allows executing the same code (e.g., for constraint validation) on the back-end and the front-end.

4. It's the only language that allows dynamic distribution, that is, executing the same code (e.g., for business computations) either in the back-end or the front-end, depending on run-time conditions such as the available front-end resources.

5. It combines object-oriented with functional programming.

6. Its dynamism allows various forms of meta-programming, which means it gives you the liberty to program your own programming concepts like classes and enumerations.

--------------------------------------------------------------------------------

For running any of the six example apps from our web server, and for getting their code, please visit <http://web-engineering.info/JsFrontendApp-CodeLinks>.
