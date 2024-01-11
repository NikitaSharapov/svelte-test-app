<script lang="ts">
	import { onMount } from 'svelte';

	// let calendarArray: Array<{data: string, contr: number}> = [];
	let dateArray1: Array<{ data: string; contr: number }> = [];
	// let test: Array<{data: string, contr: number}> = []
	const months: Array<string> = [
		'Янв',
		'Фев',
		'Мар',
		'Апр',
		'Май',
		'Июн',
		'Июл',
		'Авг',
		'Сен',
		'Окт',
		'Ноя',
		'Дек'
	];
	let monthsArray: Array<string> = [];

	function getDateArray(): Array<{ data: string; contr: number }> {
		const currentDate = new Date();
		const dateArray: Array<{ data: string; contr: number }> = [];

		for (let i = 0; i < 357; i++) {
			const newDate = new Date(currentDate.getTime() - i * 24 * 60 * 60 * 1000);
			const day = newDate.getDate();
			const month = newDate.getMonth() + 1;
			const year = newDate.getFullYear();
			dateArray.push({ data: `${year}-${month}-${day}`, contr: 0 });
		}
		return dateArray.reverse();
	}

	function getMonthOrder(data: string): number[] {
		const months: number[] = [];
		const [year, month, day] = data.split('-');
		let currM = Number(month);
		for (let i = 0; i < 12; i++) {
			currM = currM - 1;
			if (currM === 0) {
				currM = 12;
			}
			months.push(currM);
		}
		return months;
	}

	function reorderArray(arr: string[], indexes: number[]): string[] {
		console.log(arr);
		console.log(indexes);

		const reorderedArray: string[] = [];
		indexes.map((index) => {
			reorderedArray.push(arr[index - 1]);
		});
		return reorderedArray;
	}

	onMount(async function () {
		const response = await fetch('https://dpg.gg/test/calendar.json');
		const data: { [data: string]: number } = await response.json();

		const calendarArray = Object.entries(data).map((item, index) => ({
			data: item[0],
			contr: item[1]
		}));
		const dateArray = getDateArray();

		dateArray.map((dataItem, index) => {
			calendarArray.map((arrItem) => {
				if (arrItem.data === dataItem.data) {
					dateArray[index] = { ...dataItem, contr: arrItem.contr };
				}
			});
		});

		dateArray1 = dateArray;
		const currDate = dateArray.slice(-1)[0].data;
		const monthOrder = getMonthOrder(currDate);

		monthsArray = reorderArray(months, monthOrder);
	});
</script>

<div class="container">
	<div class="month-container">
		{#each monthsArray as item}
			<span class="month-title">{item}</span>
		{/each}
	</div>
	<div class="item-container">
		<div class="day"><span class="day-title">Пн</span></div>
		<div class="day"></div>
		<div class="day"><span class="day-title">Ср</span></div>
		<div class="day"></div>
		<div class="day"><span class="day-title">Пт</span></div>
		<div class="day"></div>
		<div class="day"></div>

		{#each dateArray1 as item, index}
			<div
				class="item tooltip {item.contr > 30
					? 'item-color5'
					: item.contr > 20 && item.contr < 29
						? 'item-color4'
						: item.contr > 10 && item.contr < 19
							? 'item-color3'
							: item.contr > 1 && item.contr < 9
								? 'item-color2'
								: 'item-color1'}">
				<span class="tooltiptext">{item.contr === 0 ? 'No contributions': `${item.contr} contributions`}<span class="tooltiptext-date">{new Date(Date.parse(item.data)).toLocaleDateString('ru-RU', {day: "numeric", month: "long", year: "numeric"})}</span></span>
			</div>
		{/each}
	</div>
	<div class="info-container">
		<span class="info-title">Меньше</span>
		<div class="info-list">
			<div class="info-item item-color1 info-tooltip">
				<span class="tooltiptext">No contributions</span>
			</div>
			<div class="info-item item-color2 info-tooltip">
				<span class="tooltiptext">1-9 contributions</span>
			</div>
			<div class="info-item item-color3 info-tooltip">
				<span class="tooltiptext">10-19 contributions</span>
			</div>
			<div class="info-item item-color4 info-tooltip">
				<span class="tooltiptext">20-29 contributions </span>
			</div>
			<div class="info-item item-color5 info-tooltip">
				<span class="tooltiptext">30+ contributions </span>
			</div>
		</div>
		<span class="info-title">Больше</span>
	</div>
</div>
<!-- <div class="container">

  {#each test as month}
      <div class="item-container">
    <div class="month-container">
      <span>{month.month}</span>
    </div>
      <div class="item-list">
            {#each month.dates as item, index}
       <div class="item"></div>
    {/each}
      </div>
  </div>
  {/each}
</div> -->

<style>
  @import url('https://fonts.googleapis.com/css2?family=Inter&display=swap');
	.container {
    font-family: 'Inter', sans-serif;
		display: grid;
		margin: 60px auto;
		width: 920px;
	}
	.month-container {
		display: grid;
		grid-template-columns: repeat(12, 1fr);
		justify-items: center;
	}

	.month-title {
		font-size: 12px;
		color: #959494;
	}

	.item-container {
		display: grid;
		grid-template-rows: repeat(7, auto);
		grid-auto-flow: column;
		gap: 2px;
	margin-top: 5px;
	}

	.item {
		width: 15px;
		height: 15px;

		border: 1px solid transparent;
	}
	.item-color1 {
		background: #ededed;
	}
	.item-color2 {
		background: #acd5f2;
	}
	.item-color3 {
		background: #7fa8c9;
	}
	.item-color4 {
		background: #527ba0;
	}
	.item-color5 {
		background: #254e77;
	}
	.item:hover {
		cursor: pointer;
		border: 1px solid #c4c4c4;
	}
	.day {
		width: 15px;
		height: 15px;
    	margin-right: 5px;
	}
	.day-title {
		font-size: 12px;
		color: #959494;
	}
	.info-container {
		margin-top: 15px;
		width: 170px;
		display: grid;
		grid-template: 1fr / auto auto auto;
		align-items: center;
	}
	.info-list {
		display: grid;
		grid-auto-flow: column;
		grid-auto-columns: min-content;
		gap: 2px;
	}
	.info-title {
		font-size: 9px;
		color: #959494;
	}
	.info-item {
		width: 15px;
		height: 15px;
		border: 1px solid transparent;
	}
	.tooltip {
		position: relative;
		display: inline-block;
	}

	.tooltip .tooltiptext {
		visibility: hidden;
		width: 120px;
		background-color: black;
		color: #fff;
		text-align: center;
		font-size: 12px;
		border-radius: 3px;
		padding: 5px 0;
		position: absolute;
		z-index: 1;
		top: 150%;
		left: 50%;
		margin-left: -60px;
    display: grid;
    gap: 4px;
	}
  .tooltiptext .tooltiptext-date {
    font-size: 11px;
    color: #7C7C7C;
  }

	.tooltip .tooltiptext::after {
		content: '';
		position: absolute;
		bottom: 100%;
		left: 50%;
		margin-left: -5px;
		border-width: 5px;
		border-style: solid;
		border-color: transparent transparent black transparent;
	}

	.tooltip:active .tooltiptext {
		visibility: visible;
	}

	.info-tooltip {
		position: relative;
		display: inline-block;
	}

	.info-tooltip .tooltiptext {
		visibility: hidden;
		width: 120px;
		font-size: 12px;
		background-color: black;
		color: #fff;
		text-align: center;
		border-radius: 3px;
		padding: 5px 0;
		position: absolute;
		z-index: 1;
		bottom: 150%;
		left: 50%;
		margin-left: -60px;
	}

	.info-tooltip .tooltiptext::after {
		content: '';
		position: absolute;
		top: 100%;
		left: 50%;
		margin-left: -5px;
		border-width: 5px;
		border-style: solid;
		border-color: black transparent transparent transparent;
	}

	.info-tooltip:hover .tooltiptext {
		visibility: visible;
	}
</style>
