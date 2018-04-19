---
layout: post
---
# Who is a computer software engineer that I could interview for a school project?

Abigail Landwehr

Hi!

Thanks so much for replying to my question! My name is Abby Landwehr. I live in Powell Wyoming and was wondering if I could interview you over the phone or over email about software engineering. My project is about science in careers and I am interested in the position and field of work.

## The interview

OK, we can go

### Great. So, what is the nature of a Software Engineer? Meaning, what do you do for your job?

As you can see from my Quora credential I am currently a Continuous Integration Architect

First, CI is part of the development cycle where the application gets built whenever one of the developers checks his code in.

The idea is that it gets built and tested each time so that we are quickly aware of any problems. We are not really using this properly because the build step takes too long at present. So part of my job is to develop a new process that allows us to do builds and tests much more quickly. I'll come back to that if I may.

I am also the CI administrator, which means that I do the support and training of new developers.

The interesting part of my job is the design of the new process. We can spend the rest of the interview talking about that, so let us come back to it if there is time.

### Sure, that sounds very interesting. I would like to come back to that. If I may also ask, what degrees have you received that help you in your profession?

I have a BSc in Computer Science. My circumstances did not allow me to study further.

### How long did it take to get your degree?

I had a 5 year scholarship, of which two years were at an English college to study for A-Levels required for British university entry, then three years on the BSc program at Loughborough University near Nottingham in the midlands. Loughborough is one of the best engineering universities in Britain.

### That's very impressive. What company do you work for?

I work for [Ecentric Payment Systems](http://www.ecentric.co.za/). We do all sorts of payment related things. A basic service is card payment processing for retailers. We also do online payments like Paypal and money transfers. The last is very important in a partially developed country where there are many people without proper banking. Our main value added product is payment reconciliation where we reconcile the card transactions against the bank statements etc, and providing information about payments to our customers.

We have recently been bought out by one of the big banks, and we do some systems for them. The best retail banking reconciliation system in the industry is our application, so we are quite popular with the bank.

### That's very cool.

### What do you find are the benefits to your career?

The most interesting and rewarding part is the many different industries I have worked in: Mining, Central Government Finance, Logistics, Provident and Insurance, Payments, and all sorts of others like church finances and geographic information for a company that paints road markings.

The other is that I really enjoy programming. When I was at school I was interested in maths and physics, but when I had the opportunity to do computer science I chose that. There were no PCs in those days. When I discovered what programming entailed, I decided that was for me.

### What can beginners expect out of the job when entering at an entry level position?

It depends on the beginners background and education. So for a CS, CE or IT graduate, most will be on graduate integration programs  my first job title after graduating was Graduate Improver. Somebody with another degree and no work experience will have to get programming training, so the path will be longer. Those people will be on a Management/Specialist track.

People who have been to a trade school will most likely start as junior programmers if they did that.

People without computer related training will mostly start as business analysts. We usually recruit them from clients because the business experience is particularly important. Less qualified people from clients are recruited as testers. The business analysts are responsible for liaising between the development team and the clients, so they need to be strong communicators.

There is a strong gender divide. Only about one in ten or twenty developers are female, whereas the ratio is reversed among BAs and testers. It hasn't always been that way. I suppose programming is really nerdy.

We also have people coming in from other backgrounds. Of the two best programmers I knew, one was a geologist and the other was a wildlife biologist. The geologist was also an excellent team lead.

### Thank you. How long have you been in your career?

I graduated in 1976. I worked as a computer operator for six months before leaving Zambia to study in the UK. So 41 years, not counting university.

### How long were you in the UK?

The five years for A-levels and university.

### I'm sorry I'm not familiar with A-levels. What are those?

The British education system has the usual 10 or 12 years leading to O-levels or GCE. O-levels (Ordinary) are your final exams on finishing school. GCE is more trade related, like carpentry. ALevels (Advanced) basically cover the first year or so of college or university in other countries. So in South Africa my 3-year BSc course would have taken four or five years. I expect it is similar in the US.

### In your job, what major scientific discoveries have had an effect on your career?

I would say that it was general development. In the beginning there were only mainframe computers, and they were really hard to program. For example, we had to do all the things that are now done with database management systems. I've just thought  that could be seen as a scientific discovery. It is very slow when you see them happen☺. OK, so databases, the internet, the WWW, fibre optics and lasers, the whole Moore's Law thing, personal computers and laptops, high capacity hard drives, solid state drives. I'm sure there were more like geosynchronous communications satellites, but who is counting?

From my point of view software development methodologies have advanced tremendously, and the software tools that we have available to us. The weird thing is that we are still struggling with things that should be simple or at least well understood. There is still much to be done.

### What would you consider to be the simple things that we are still struggling with in our development?

The biggest thing is that we still seem to be inventing the development process. We talk about best practice, but our practice is still unreliable. Things like cost control and on-time delivery is a big issue. Even rocket science is more advanced than we are, in that once the rocket works, it can work for years without incident. 

In a sense it is in the nature of the profession. SE has more in common with design than manufacturing, so each application we develop is like a new model car with a new model engine developed at the same time. See the problems Tesla is now having with the Model 3. That happens to us all the time.

### Could you tell me more about your designing of a new process? 

Okay, let us talk about what I am doing now. I took over the role about 18 months ago and to start with I had to become thoroughly familiar with the process. For the last year or so I have been working on the database build process, our biggest bottleneck. The problem is that Microsoft does not provide the relevant tools for the job, unlike for Web development where they have excellent tools. I was pointed to a particular technology that I was unfamiliar with which had promise. I spent a few months studying the documentation and doing some prototyping to get to understand the technology.

Once I was confident I used it to speed up the build process, but that has proved to be unreliable and has not really helped. 

My next idea was to use the Microsoft application packaging and versioning technology. This is used primarily for add-ins to the Visual Studio IDE (Interactive Development Environment), and also by companies like ours for in-house components.

The problem is that Microsoft does not have support for that technology for database projects. I had read a comment by one of their other clients of how they dealt with the problem - these people were just as interested as I in Microsoft providing the technology.

So my aim at present is to provide the missing functionality as nearly like the Microsoft solution. This is for two reasons: it is what the developers are familiar with, and when Microsoft eventually build this technology we should be able to replace my stuff smoothly.

So my process consists of a lot of reading on the internet, a lot of prototyping, and a lot of trial and error. My job is allowed to be complicated, but what I finally deliver must be easy to use.

Because I am the only developer, we don't have to spell out the requirements to the same degree as would be needed for a team project. This gives me greater freedom to postpone decisions as long as possible. I still need to have a clear vision, there has to be some idea of the time it will take so that management can make decisions, and I have the obligation do document what I do (Yuck!)

### I agree. In the computer science class I'm currently taking, we have to list out our steps in a document and as algorithms. Do you use algorithms when creating code as well?

Not so much. Most of the algorithms, like hash tables / dictionaries are in the system. You do need to know them to use them effectively. Mostly I import and export data, and manipulate it. This all has to be organized, which is where program design comes in.

### I appreciate your time today. Thank you so much. This will help me with my assignment and now I have more practical knowledge about the career.

My pleasure.