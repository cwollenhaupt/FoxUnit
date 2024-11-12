## FoxUnit code sample

Notice that each **Test Class** can contain multiple **Tests**, which are simply individual procedures defined within the class.


	Define Class CustomerTestClass As FxuTestCase Of FxuTestCase.prg

		oObject = .Null.

		Procedure Setup
		This.oObject = Createobject("cCustomerClass")
		Endproc

		Procedure testGetCustomer
			llReturn = This.oObject.GetCustomer("AKSEL")
			This.AssertTrue(llReturn, "Customer number not found.")
			If llReturn
				This.MessageOut("Customer name is  " + This.oObject.CustName)
			Endif
		Endproc

		Procedure testRaiseCreditLimit
			loCustomer = This.oObject
			lnOriginalCreditLimit = loCustomer.oData.credit_limit
			loCustomer.SetCreditLimit(lnOriginalCreditLimit + 100)
			This.AssertTrue(loCustomer.oData.credit_limit > lnOriginalCreditLimit, "Error: Credit limit not increased.")
		Endproc
		
		Procedure TearDown()
		&& Add any cleanup code here.
		Endproc
	EndDefine

Each Test can be turned into a **Theory** by adding an array to the class that has the same name as the test and the suffix "_data".

	Define Class CustomerTestClass As FxuTestCase Of FxuTestCase.prg

		Dimension testTheory_Data[3,2]
		testTheory_Data[1,1] = 1
		testTheory_Data[1,2] = 2
		testTheory_Data[2,1] = 3
		testTheory_Data[2,2] = 6
		testTheory_Data[3,1] = 5
		testTheory_Data[3,2] = 10

		Procedure testTheory (tnValue, tnResult)
			this.AssertEquals (m.tnResult, 2*m.tnValue)
		EndProc

	EndDefine

Every row in the array contains on theory and corresponds with one set of parameters for the test. The values from each column are passed as parameters to the test in order. The test is executed once per row. 

Every value in a theory can be replaced with a list of values. FoxPro does not support nested arrays or collection initilizers in a class definition. Therefore every valiu of a theory that is a list is defined as an array with the same name as the test method followed by the suffix "_data" followed by the row and column of the theory array. Both values are separated by an underscore. FoxUnit executes each theory with all **Permutations** of parameters.

	Define Class CustomerTestClass As FxuTestCase Of FxuTestCase.prg

		Dimension testTheorySeries_Data[2,3]
		Dimension testTheorySeries_Data_1_1[5]
		testTheorySeries_Data_1_1[1] = "   Hello"
		testTheorySeries_Data_1_1[2] = "Hello   "
		testTheorySeries_Data_1_1[3] = "HELLO"
		testTheorySeries_Data_1_1[4] = ",Hello:"
		testTheorySeries_Data_1_1[5] = "Hello World"
		Dimension testTheorySeries_Data_1_2[3]
		testTheorySeries_Data_1_2[1] = 1
		testTheorySeries_Data_1_2[2] = 2
		testTheorySeries_Data_1_2[3] = 3
		testTheorySeries_Data[1,3] = "HELLO"

		testTheorySeries_Data[2,1] = "Second row"
		testTheorySeries_Data[2,2] = 4
		testTheorySeries_Data[2,3] = "SECOND"

		Procedure testTheorySeries (tcValue, tnPass, tcResult)
			This.MessageOut (Transform(m.tnPass)+", "+m.tcValue)
			This.AssertEquals (m.tcResult, Upper(GetWordNum(m.tcValue,1,",: ")))
		EndProc
	
	EndDefine

The test above defines two theories. The first theory has five values for the first parameter (tcValue) and three values for the second parameter (tnPass). This means the first theory is tested for a total of 15 combinations. The second theory only has one set of values. That means the "testTheorySeries"-test is executed 16 times in total.

**Asserts**
These Assert methods can be used to test actual values against expected values, with output text to be displayed when the Assert statement is not met.

	AssertEquals(tuExpectedValue, tuExpression, tcMessage,tuNonCaseSensitiveStringComparison)
	(AssertEquals now accepts objects as well as scalar variables)
	AssertEqualsArrays(@taArray1, @taArray2, tcMessage)
	AssertNotEqualsArrays(@taArray1, @taArray2, tcMessage)

	AssertTrue(tuExpression, tcMessage)
	AssertFalse(tuExpression, tcMessage)
	AssertNotEmpty(tuExpression, tcMessage)
	AssertNotNull(tuExpression, tcMessage)
	AssertNotNullOrEmpty(tuExpression, tcMessage)

	AssertIsObject(toObject, tcMessage)
	AssertIsNotObject(toObject, tcMessage)

	AssertHasError(tcMessage, toException, taStackInfo)
	AssertHasErrorNo(tcMessage, toException, tnErrorNo, taStackInfo)

	AssertNotImplemented(tcMessage)  (The new default return value for new tests)


**Other methods**
Additionally, these methods are frequently used when creating tests:


	SetUp() && Called automatically at beginning of each test
	TearDown() && Called automatically at the end of each test
	MessageOut(tcMessage) && Message will be displayed in the UI


* **Setup()** method should contain any common code that is needed for each test -- such as setting up paths or instantiating objects.
* **TearDown()** method contains any cleanup code that should be run at the end of each test method.
* **MessageOut()** - Add any text output from your test methods that you wish to display in the Messages pane of the FoxUnit UI.