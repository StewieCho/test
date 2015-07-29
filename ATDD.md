# ATDD

### Cucumber Library
```
     <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>info.cukes</groupId>
            <artifactId>cucumber-java</artifactId>
            <version>1.2.3</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>info.cukes</groupId>
            <artifactId>cucumber-junit</artifactId>
            <version>1.2.3</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

### Cucumber Eclipse Plugin
https://cucumber.github.com/cucumber-eclipse

### Step
##### feature 파일 생성
```
Feature: 단기 주차장
주차 요금 계산기는 단기 주차장의 주차 요금을 계산할 수 있다.
	
Scenario Outline: 소형차량, 단기 주차장의 비수기 주차 기본 요금을 계산한다.
When 내가 비수기 <isWeekend>에 단기 주차장에 소형차를 <duration>분 동안 주차했다.
Then 나는 주차비로 <cost>원을 지불해야 한다.
Examples:
|isWeekend| duration | cost|
|"false"|29|1200|
|"false"|30|1200|
|"false"|31|1800|
|"false"|45|1800|
|"false"|46|2400|
|"false"|300|12000|
|"false"|301|12000|
|"false"|1440|12000|
|"false"|1441|12600|
|"false"|1739|24000|
|"false"|2880|24000|
|"true"|29|1400|
|"true"|30|1400|
|"true"|31|2100|
|"true"|45|2100|
|"true"|46|2800|
|"true"|300|14000|
|"true"|301|14000|
|"true"|1440|14000|
|"true"|1441|14700|
|"true"|1739|28000|
|"true"|2880|28000|


```
##### TestCase 생성
```java
package com.inchon.parking;

import org.junit.runner.RunWith;
import cucumber.api.junit.Cucumber;

@RunWith(Cucumber.class)
public class ShortTermParkingLotTest {
}

```

##### testcase 실행 => sample 코드 printout
##### ShortTermParkingLotStepDefinition 생성
```java
package com.inchon.parking;

import static org.hamcrest.CoreMatchers.equalTo;
import static org.hamcrest.MatcherAssert.assertThat;
import cucumber.api.java.en.Then;
import cucumber.api.java.en.When;

public class ShortTermParkingLotStepDefinition {

	private int duration;
	private boolean isWeekend;

	@When("^내가 비수기 평일에 단기 주차장에 소형차를 (\\d+)분 동안 주차했다\\.$")
	public void 내가_비수기_평일에_단기_주차장에_소형차를_분_동안_주차했다(int duration)
			throws Throwable {
		this.duration = duration;
	}

	@Then("^나는 주차비로 (\\d+)원을 지불해야 한다\\.$")
	public void 나는_주차비로_원을_지불해야_한다(int cost) throws Throwable {

		assertThat(new ParkingCostCalculator().calculate(isWeekend, duration),
				equalTo(cost));
	}
	
	@When("^내가 비수기 \"([^\"]*)\"에 단기 주차장에 소형차를 (\\d+)분 동안 주차했다\\.$")
	public void 내가_비수기_에_단기_주차장에_소형차를_분_동안_주차했다(String isWeekendStr, int duration) throws Throwable {
		isWeekend = Boolean.valueOf(isWeekendStr);
		this.duration = duration;
		
	}

}
```
