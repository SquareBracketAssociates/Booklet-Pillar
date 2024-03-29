## Testing Your Documents What does it mean to ‘test a book’ and why would you do it? Testing a book means checking the code it displays is up to date. This can be done by checking if examples are correct or verifying that method and class definitions compile without raising an error in the last Pharo version. Being able to test a book means updating it more easily and therefore more frequently. On the long term, easier updates means more frequent updates but also a documentation improvement along with more ressources available for the community.Pillar will come with a book tester. The described features will be available in the future version. ### Typical CodeblocksPillar syntax allows you to write codeblocks, showing a part of code. The body of a codeblock allows one to display any part of code you might want to. For instance, example, method and class definitions can be tested.#### Examples```   [[[
	   1 + 1
	>>> 2
   ]]]```#### Method DefinitionThis codeblock presents a method definition.```  [[[
	YourClass >> yourMethod
		^ 'bla'
  ]]]```#### Class DefinitionThis codeblock presents a class definition.```   [[[
   Object subclass: #YourClass
        instanceVariableNames: ''
        classVariableNames: ''
        package: 'YourPackage'
   ]]]```As we show next, using parameters we can indicate that the expression inside a codeblock should be validated### Testing Codeblock Examples Using the parameter `example` and the `>>>` helps you to validate your examples. The book tester follows the following pattern with only one `>>>` per codeblock.```    [[[example=true
       Action 
    >>> ExpectedResult
    ]]]```For example `example=true` declares that there codeblock is a testable example. The marker `>>>` is used to identify the result. ```   [[[example=true
   3 + 4
   >>> 7
   ]]]```The book tester interprets the previous parametrized codeblock as the assertion  `self assert: (3 + 4 ) equals: 7`. ### Testing method and class definitionsThe booktester checks that class and method definitions are valid \(do not raise syntactical errors at compilation\). You should use the following parameters:  `methodDefinition=true` or classDefinition=true as code block. It makes sure your book is up to date with the latest Pharo version.#### Method definitionsMethod definitions should follow this format:```	[[[methodDefinition=true
		YourClass >> yourMethod
		methodBody
	]]]```with an emphasis laid on the `>>` between your class and method.It is important to understand that the class \(here `YourClass`\) should be present in the system producing the Pillar book. To load code, see the `${loader}$` annotation presented in the following chapter.#### Class definitions Class definitions should follow this format:```   [[[classDefinition=true
	   YourSuperClass subclass: #YourClass
          instanceVariableNames: ''
          classVariableNames: ''
          package: 'YourPackage'
	]]]```In addition to validate that the class definition is valid, the parameter defines the class in the environment in which the book is defined. It supports the definition of testing other methods. As a general principle, any method definition refering to classes not defined in the system, should be defined after by a class definition or a loading instruction \(see `$loader$` as shown in the following\).### Practical testingThere are currently two ways of testing your book: \(1\) using the command line and \(2\) from within Pharo.Now from the command line two options are supported. #### Check file```language=bash> $PHARO_VM $IMAGE clap checkFile yourfolder/f1.pillar```When a path is not explicitely stated, the book tester takes the first file matching `*.pillar`.!!todo Add the help output.#### Check all files```language=bash> $PHARO_VM $IMAGE clap checkRepository yourfolder/f1.pillar```When a path is not explicitely stated, the book tester takes the first file matching `*.pillar`.```language=bash> $PHARO_VM $IMAGE clap checkRepository --help```#### Check full book```language=bash> $PHARO_VM $IMAGE pillar build checkBook --baseDirectory=`pwd````This second version uses the already existing `build` command for Pillar. Pay attention the key difference is that the build version works on expanded text version where annotations have been processed while the clap version only on the pillar textual input.However, using one method or the other leads you to obtaining a report on all tests made. The passed ones are not displayed, however the failed ones are shown by stating first the explanation of the failure \(name of the exception or if the assertion failed\). Note that only codeblocks with one of the three following parameters will be tested: `example`, `methodDefinition` and `classDefinition`. And they will be tested using the methods used above.!!todo Add a report ### From within PharoYou can also test a pillar file from within Pharo itself. To do so you should use a `PRBookTesterVisitor` to check a file using `checkFileNamed:`. This can be useful to inspect each of the results. In particular the successful one.### What can not be testedSome codeblocks can not be tested. For example, nested results to show what happens after some iterations such as:```	[[[
		1 + 1
		>>> 2
		>>> 3
		>>> 4
	]]]```Local variable definition are not validated:```	[[[
		|tmp|
		tmp := 0.
		tmp >>> 0
	]]]```Or examples that show an error should be raised such as:```	[[[
		String \+
		>>> Error
	]]]```Or examples without concrete values```	[[[
		Date today
		>>> aDate
	]]]```with `yourAge` and `myAge` as variables for example. ## Improving Book WritabilityBookTester can do a lot more for you. Indeed you can load a specific version of your code and display it instead of copying it from your code repository to your pillar. In addition you can run your tests.### Loading your codeHow to test something you have defined but not loaded? This certainly is the question, especially when using class or methods that you just defined \(which happens in nearly every book you might want to write\).To answer that question, the following annotation has been implemented:```${loader:account=YourGitAccount|
	project=YourGitProject|
	tag=YourGitTag|
	baseline=YourBaselineName}$```This annotation loads your git project at the given tag. The tag is useful to specify your chapter's prerequisites \(precedent chapter for example\). Note that a proper use of this annotation requires you to tag every step of your code to create milestones. The baseline parameter takes by default the name of the project, but it is present in case they are different.### Support test-driven development writingWhen writing book to teach TDD, authors often place test first and only after method definitions. This is why validating a test just after its definition may be not work. This is why every codeblock with the parameter `methodDefinition` has its compilation tested but a test will not be run until the `run` annotation is encountered.```${run:testClass=YourTestClassName}$```The run annotation will run every test defined with `methodDefinition` within a given test class and report the results in the booktester report.#### Typical TDD booklet A typical sequence will be then:```   [[[classDefinition=true
	   TestCase subclass: #MyTestCase
   ]]]

   [[[methodDefinition=true
       MyTestCase >> testFoo
             self assert: Foo new name equals: 'foo'
   ]]]
   
   [[[methodDefinition=true
      MyTestCase >> testFoo2
        self assert: Foo new friendName equals: 'bar'
   ]]]
   
    [[[classDefinition=true
		Object subclass: #Foo
   ]]]

   [[[methodDefinition=true
	   Foo >> name 
        ^ 'foo'
  ]]]

     ${run:testClass=MyTestCase}$

   [[[methodDefinition=true
  	   Foo >> friend 
          ^ 'bar'
    ]]]
	
    ${run:testClass=MyTestCase}$```   #### About method definitionIt is possible to define a method that will be used in a test but without showing its definition in the text using the `hidden` parameter. For example, when the author does not want to show accessors but want to use them in tests.The following method will be compiled to the class `Foo` but will not be shown in the book output.```	[[[methodDefinition=true,hidden=true
		Foo >> addedButNotShown
			^42 
	]]]```### Helping ToolsIn addition to the previous annotations, book tester offer three other annotations: `showClass`, `showMethod`, and `screenshot`.#### showClassThe `showClass` annotation allows you to display the class definition of your choice and is used as follows```${showClass:class=YourClassName}$```For example, using the annotation like this:```${showClass:class=Integer}$```displays the following in the text```Number subclass: #Integer
	instanceVariableNames: ''
	classVariableNames: ''
	package: 'Kernel-Numbers'```Note that currently the displayed class definition is not parametered as `classDefinition` because we supposed there is no need to test the compilation of it. Whether you defined it somewhere else and want to show it again or it is already defined in the system and therefore does not need to be compilation-tested.#### showMethodThe `showMethod` annotation allows you to display the class definition of your choice and is used as follows```${showMethod:method=isPowerOfTwo|class=Integer}$```displays the following method in the document:```Integer>>isPowerOfTwo
	"Return true if the receiver is an integral power of two."
	^ self ~= 0 and: [(self bitAnd: self-1) = 0]```For the same reasons as `showClass`, the displayed method definition is not parametered as `methodDefinition`.#### screenshotThe `screenshot` annotation is used as follows:```${screenshot:class=YourClassName|method=yourMethodName|caption=yourCaption|width=yourWidth|label=yourLabel}$```This annotation uses the class and method names to create both:- **PNG File** corresponds to a System Browser opened on the given method definition found in the given class method dictionary ; this file is stored under YourChapter/figures/screenshot/YourClassName>>YourMethodName-Date.png - **Figure Reference** replaces the annotation with the fresh created PNG file and given caption/width/labelThe default value of `caption` is `YourClassName>>YourMethodName`. The default value of `label` is `lbl_yourMethodName`. The default value of width is `50`.### ConclusionThis chapter shows that Pillar offers strong support to synchronise your code and its documentation. First it provides ways to test that examples or definitions are correct. Second it offers ways to link your versioned code can be loaded and displayed. Finally, even tests in the code loaded can be verified.