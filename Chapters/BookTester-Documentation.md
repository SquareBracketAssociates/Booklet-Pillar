## Testing Your Documents
	   1 + 1
	>>> 2
   ]]]
	YourClass >> yourMethod
		^ 'bla'
  ]]]
   Object subclass: #YourClass
        instanceVariableNames: ''
        classVariableNames: ''
        package: 'YourPackage'
   ]]]
       Action 
    >>> ExpectedResult
    ]]]
   3 + 4
   >>> 7
   ]]]
		YourClass >> yourMethod
		methodBody
	]]]
	   YourSuperClass subclass: #YourClass
          instanceVariableNames: ''
          classVariableNames: ''
          package: 'YourPackage'
	]]]
		1 + 1
		>>> 2
		>>> 3
		>>> 4
	]]]
		|tmp|
		tmp := 0.
		tmp >>> 0
	]]]
		String \+
		>>> Error
	]]]
		Date today
		>>> aDate
	]]]
	project=YourGitProject|
	tag=YourGitTag|
	baseline=YourBaselineName}$
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
	
    ${run:testClass=MyTestCase}$
		Foo >> addedButNotShown
			^42 
	]]]
	instanceVariableNames: ''
	classVariableNames: ''
	package: 'Kernel-Numbers'
	"Return true if the receiver is an integral power of two."
	^ self ~= 0 and: [(self bitAnd: self-1) = 0]