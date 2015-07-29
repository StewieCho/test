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
