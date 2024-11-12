## FoxUnit 代码示例

注意：每个 **测试类(Test Class)** 都可以包含多个 **测试(Tests)**, 你只需在类中定义不同的过程即可。


	Define Class CustomerTestClass As FxuTestCase Of FxuTestCase.prg

		oObject = .Null.

		Procedure Setup
		This.oObject = Createobject("cCustomerClass")
		Endproc

		Procedure testGetCustomer
			llReturn = This.oObject.GetCustomer("AKSEL")
			This.AssertTrue(llReturn, "未找到客户编号。")
			If llReturn
				This.MessageOut("客户编号为：  " + This.oObject.CustName)
			Endif
		Endproc

		Procedure testRaiseCreditLimit
			loCustomer = This.oObject
			lnOriginalCreditLimit = loCustomer.oData.credit_limit
			loCustomer.SetCreditLimit(lnOriginalCreditLimit + 100)
			This.AssertTrue(loCustomer.oData.credit_limit > lnOriginalCreditLimit, "错误：信用额度未提高。")
		Endproc
		
		Procedure TearDown()
		&& 请在此处添加任何清理代码。
		Endproc
	EndDefine

通过在类中添加一个与测试名称相同、后缀名为“_data ”的数组，可以将每个测试转化为**理论**。

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

数组中的每一行都包含一个理论，对应一组测试参数。每一列的值作为参数依次传递给测试。每行测试执行一次。

理论中的每个值都可以用一个值列表代替。FoxPro 不支持类定义中的嵌套数组或集合启动器。因此，理论中的每一个列表值都被定义为一个数组，其名称与测试方法相同，后缀为“_data”，然后是理论数组的行和列。两个值之间用下划线分隔。FoxUnit 在执行每个理论时，会同时执行参数的所有 ** 变体**。

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

上述测试定义了两个理论。第一个理论的第一个参数（tcValue）有五个值，第二个参数（tnPass）有三个值。这意味着第一个理论总共测试了 15 种组合。第二个理论只有一组值。这意味着 “testTheorySeries ”测试总共执行了 16 次。

**Asserts**
这些 Assert 方法可用于根据预期值测试实际值，并在不满足 Assert 语句时显示输出文本。

	AssertEquals(tuExpectedValue, tuExpression, tcMessage,tuNonCaseSensitiveStringComparison)
	(AssertEquals 现在接受对象和变量)
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

	AssertNotImplemented(tcMessage)  (新测试的新默认返回值)


**其他方法**
此外，在创建测试时经常使用这些方法：


	SetUp() && 在每次测试开始时自动调用
	TearDown() && 在每次测试结束时自动调用
	MessageOut(tcMessage) && 在 UI 中显示消息


* **Setup()** 方法应包含每个测试所需的任何公共代码 - 例如设置路径或实例化对象。
* **TearDown()** 方法包含应在每个测试方法结束时运行的任何清理代码。
* **MessageOut()** - 用于任何你想在 FoxUnit UI 的消息面板中显示的任何的文本。
