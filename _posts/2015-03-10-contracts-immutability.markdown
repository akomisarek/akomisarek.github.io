---
layout: post
title:  "Contracts, documentation, immutability."
date:   2015-03-10 21:45:26
categories: development
tldr: Contracts matters, check documentation for everything you use, try immutability!
---
I wanted to share why contracts matters, why you should always should check documentation (even for things you might think you are familiar with) and that immutability is good - short examples I think are interesting and maybe they will help you avoid bugs in future. Great, let's roll on!

(If you are familiar with natural ordering contracts, *HashMaps* contract and Guava view you can stop reading now! Nothing magical is incoming.)

So let's start why contracts matter and why you should check documentation. Everyone knows that's important but yet in my experience it is overlooked! And then where the big surprises come. 

Here is really simple example why not knowing exact contract of class you are using may break the system.  

# Set story

You have simple Person class with first name and last name fields. Every person has some unique identifier. You wrote equals and hash code implementation. You want to store people uniquely so you have a *Set*.

	Set<Person> persons = new HashSet<>();
	persons.add(adamK);
	persons.add(adamk);
	persons.add(johnS);

	//What is the size of the persons Set? - obviously 2
	Assert.assertEquals("There are 2 unique persons - AdamK and JohnS", 2, persons.size());

Now let's spice it up a bit. The boss comes and "asks" you about providing the sorted order of persons. You implement the *Comparable<Person>* (or create *Comparator<Person>* it doesn't really matter, but the latter is better design)) and change the Set implementation to *TreeSet*. Ah and one more thing, there comes second John Smith so you add it to list. 

	Set<Person> persons = new TreeSet<>(); //or optional Comparator
	persons.add(adamK);
	persons.add(johnS1);
	persons.add(johnS2);

	//What is the size of the persons Set? - obviously 3
	Assert.assertEquals("There are 3 unique persons - AdamK and two JohnSs", 3, persons.size()); //BOOM!

The test fails. Why is that? I have 3 different persons in my domain?! Why on set are there only two of them. Here where the contract on *TreeSet* comes into play: 

```
Note that the ordering maintained by a set (whether or not an explicit) must be consistent with equals if it is to correctly implement the Set interface.
```

So know everything is a bit more clear. They are not using equals for the *TreeSet* but compareTo! This is different than *HashSet*, but at least they are clear about it in documentation.   

#Map story

Let’s look into another subtle example. 

You have objects and want to keep it in map. You have following piece of pseudo-code:

	Map<MyObject, ImportantData> map = new HashMap<>();
	MyObject object = new MyObject();
	object.setData(importantData);

	map.put(object, importantData);  

	alienObject.alienMethod(object); //this alien method called on object you didn't write can do nasty things to your object

	...
	ImportantData currentData = map.get(object);
	currentData.play();

What may be surprising if you are not familiar with *HashMap* and how hashing works and are used to objects references (hey I am passing the same object to get and put) you may get *NullPointerException*! The problem is, yes your object is on the map, but it lies in bucket that is addressed using object's hash code. If the hash code of object changed after it was added to the map, the lookup will fail. Just don't use mutable elements as keys for hash map and you will be safe! You should really try immutability, it makes not only multi-threaded code safer, but even the *simple* code is easier to reason about. 

You need to be really careful if you understand and can reason about classes you use - the most number of bugs come from interactions between components or from not fully understanding classes we use.


#View story

Last story happened to me lately. I delivered method, which was working on external list to modify one of the fields in certain circumstances (mutability, argh!). So here is the simple example:

	public void process(List <MyObject> elements) {
		for (MyObject myObject: elements) {
			if (shouldBeModified(myObject)) {
				myObject.setField("x"); //initially we have got "a" everywhere
			}
		}
	}

Of course I wrote unit tests for it, it worked, no issues with that. Then when I was running integration tests I have noticed it is not working as intended. The elements on the list had still "a" in places where there should be "x". Can you guess what was the problem? It was mentioned at the beginning: Guava view! The list I received for the process was "created" this way:

	return Lists.transform(otherElements, new Function<OtherObject, MyObject>() {
			@Override
			public MyObject override(OtherObject input) {
				MyObject newMyObject = new MyObject();
				newMyObject.setField(input.getField());
				return newMyObject;
			}
		});

Why this is a problem? The elements on the list I was updating were in fact created every time from scratch so my changes were not reflected at all! Obviously the list should be produced once and the elements created only once and not transformed on every access. If the immutable objects were used it wouldn't be a problem as well. 


I hope my samples will be useful for someone and some time will be saved. To summarize: know contracts; understand the code and favour immutability! There will be fewer bugs around you.

