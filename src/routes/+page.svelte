<script>
	import { writable } from 'svelte/store';
	let showModal = false;
	async function saveAs() {
		var fileName = prompt('Choose a file name...');
		if (fileName) {
			const response = await fetch('https://tugbrands.com/scheduler/api/schedules', {
				method: 'POST',
				body: JSON.stringify({ id: fileName, schedule: JSON.stringify(data) }),
				headers: {
					'content-type': 'application/json'
				}
			});
			let results = await response.json();
			if (results) {
				file = fileName;
				alert('File sucessfully saved...');
			} else {
				alert('There was an error saving the file...');
			}
		}
	}

	async function save() {
		if (!file) {
			var fileName = prompt('Choose a file name...');
			if (fileName) {
				const response = await fetch('https://tugbrands.com/scheduler/api/schedules', {
					method: 'POST',
					body: JSON.stringify({ id: fileName, schedule: JSON.stringify(data) }),
					headers: {
						'content-type': 'application/json'
					}
				});
				let results = await response.json();
				if (results) {
					file = fileName;
					alert('File sucessfully saved...');
				} else {
					alert('There was an error saving the file...');
				}
			}
		} else {
			const response = await fetch(`https://tugbrands.com/scheduler/api/schedules/${file}`, {
				method: 'PATCH',
				body: JSON.stringify({ schedule: JSON.stringify(data) }),
				headers: {
					'content-type': 'application/json'
				}
			});

			let results = await response.json();
			if (results) {
				alert('File sucessfully saved...');
			} else {
				alert('There was an error saving the file...');
			}
		}
	}

	async function load() {
		var fileName = prompt('Choose a file name...');
		if (fileName) {
			const response = await fetch(`https://tugbrands.com/scheduler/api/schedules/${fileName}`, {
				method: 'GET',
				headers: {
					'content-type': 'application/json'
				}
			});

			let results = await response.json();
			if (results.length) {
				file = fileName;
				data = JSON.parse(results[0].schedule);
			} else {
				alert('Invalid file name...');
			}
		}
	}

	function editEmp(num) {
		showModal = num;
	}
	let updated = true;
	let file = false;
	let hourTotals = writable([]);
	let click1 = false;
	let totalHours = 0;
	let days = ['monday', 'tuesday', 'wednesday', 'thursday', 'friday', 'saturday', 'sunday'];
	let times = [
		500, 515, 530, 545, 600, 615, 630, 645, 700, 715, 730, 745, 800, 815, 830, 845, 900, 915, 930,
		945, 1000, 1015, 1030, 1045, 1100, 1115, 1130, 1145, 1200, 1215, 1230, 1245, 1300, 1315, 1330,
		1345, 1400, 1415, 1430, 1445, 1500, 1515, 1530, 1545, 1600, 1615, 1630, 1645, 1700, 1715, 1730,
		1745, 1800, 1815, 1830, 1845, 1900, 1915, 1930, 1945, 2000, 2015, 2030, 2045, 2100, 2115, 2130,
		2145, 2200, 2215, 2230, 2245
	];
	let data = [];

	function handleClick(user, day, time) {
		if (!click1) {
			click1 = { user, day, time };
		} else {
			if (click1.user == user && click1.day == day && click1.time < time) {
				data[user][click1.day + 'In'] = click1.time;
				data[user][click1.day + 'Out'] = time;
				click1 = false;
				calculateHours(user);
			} else {
				click1 = { user, day, time };
			}
		}
	}

	function handleRightClick(user, day) {
		data[user][day + 'In'] = false;
		data[user][day + 'Out'] = false;
		calculateHours(user);
	}

	function calculateHours(user) {
		hourTotals.set([]);
		var total = 0;
		if (data[user].mondayIn) {
			var startHours = parseInt(data[user].mondayIn.toString().slice(0, -2));
			var startMinutes = data[user].mondayIn.toString().slice(-2);
			if (startMinutes == 15) startHours = parseInt(startHours) + 0.25;
			if (startMinutes == 30) startHours = parseInt(startHours) + 0.5;
			if (startMinutes == 45) startHours = parseInt(startHours) + 0.75;
			var endHours = parseInt(data[user].mondayOut.toString().slice(0, -2));
			var endMinutes = data[user].mondayOut.toString().slice(-2);
			if (endMinutes == 15) endHours = parseInt(endHours) + 0.25;
			if (endMinutes == 30) endHours = parseInt(endHours) + 0.5;
			if (endMinutes == 45) endHours = parseInt(endHours) + 0.75;
			total += parseFloat(
				endHours - startHours > 6.5 ? endHours - startHours - 0.5 : endHours - startHours
			);
		}
		if (data[user].tuesdayIn) {
			var startHours = parseInt(data[user].tuesdayIn.toString().slice(0, -2));
			var startMinutes = data[user].tuesdayIn.toString().slice(-2);
			if (startMinutes == 15) startHours = parseInt(startHours) + 0.25;
			if (startMinutes == 30) startHours = parseInt(startHours) + 0.5;
			if (startMinutes == 45) startHours = parseInt(startHours) + 0.75;
			var endHours = parseInt(data[user].tuesdayOut.toString().slice(0, -2));
			var endMinutes = data[user].tuesdayOut.toString().slice(-2);
			if (endMinutes == 15) endHours = parseInt(endHours) + 0.25;
			if (endMinutes == 30) endHours = parseInt(endHours) + 0.5;
			if (endMinutes == 45) endHours = parseInt(endHours) + 0.75;
			total += parseFloat(
				endHours - startHours > 6.5 ? endHours - startHours - 0.5 : endHours - startHours
			);
		}
		if (data[user].wednesdayIn) {
			var startHours = parseInt(data[user].wednesdayIn.toString().slice(0, -2));
			var startMinutes = data[user].wednesdayIn.toString().slice(-2);
			if (startMinutes == 15) startHours = parseInt(startHours) + 0.25;
			if (startMinutes == 30) startHours = parseInt(startHours) + 0.5;
			if (startMinutes == 45) startHours = parseInt(startHours) + 0.75;
			var endHours = parseInt(data[user].wednesdayOut.toString().slice(0, -2));
			var endMinutes = data[user].wednesdayOut.toString().slice(-2);
			if (endMinutes == 15) endHours = parseInt(endHours) + 0.25;
			if (endMinutes == 30) endHours = parseInt(endHours) + 0.5;
			if (endMinutes == 45) endHours = parseInt(endHours) + 0.75;
			total += parseFloat(
				endHours - startHours > 6.5 ? endHours - startHours - 0.5 : endHours - startHours
			);
		}
		if (data[user].thursdayIn) {
			var startHours = parseInt(data[user].thursdayIn.toString().slice(0, -2));
			var startMinutes = data[user].thursdayIn.toString().slice(-2);
			if (startMinutes == 15) startHours = parseInt(startHours) + 0.25;
			if (startMinutes == 30) startHours = parseInt(startHours) + 0.5;
			if (startMinutes == 45) startHours = parseInt(startHours) + 0.75;
			var endHours = parseInt(data[user].thursdayOut.toString().slice(0, -2));
			var endMinutes = data[user].thursdayOut.toString().slice(-2);
			if (endMinutes == 15) endHours = parseInt(endHours) + 0.25;
			if (endMinutes == 30) endHours = parseInt(endHours) + 0.5;
			if (endMinutes == 45) endHours = parseInt(endHours) + 0.75;
			total += parseFloat(
				endHours - startHours > 6.5 ? endHours - startHours - 0.5 : endHours - startHours
			);
		}
		if (data[user].fridayIn) {
			var startHours = parseInt(data[user].fridayIn.toString().slice(0, -2));
			var startMinutes = data[user].fridayIn.toString().slice(-2);
			if (startMinutes == 15) startHours = parseInt(startHours) + 0.25;
			if (startMinutes == 30) startHours = parseInt(startHours) + 0.5;
			if (startMinutes == 45) startHours = parseInt(startHours) + 0.75;
			var endHours = parseInt(data[user].fridayOut.toString().slice(0, -2));
			var endMinutes = data[user].fridayOut.toString().slice(-2);
			if (endMinutes == 15) endHours = parseInt(endHours) + 0.25;
			if (endMinutes == 30) endHours = parseInt(endHours) + 0.5;
			if (endMinutes == 45) endHours = parseInt(endHours) + 0.75;
			total += parseFloat(
				endHours - startHours > 6.5 ? endHours - startHours - 0.5 : endHours - startHours
			);
		}
		if (data[user].saturdayIn) {
			var startHours = parseInt(data[user].saturdayIn.toString().slice(0, -2));
			var startMinutes = data[user].saturdayIn.toString().slice(-2);
			if (startMinutes == 15) startHours = parseInt(startHours) + 0.25;
			if (startMinutes == 30) startHours = parseInt(startHours) + 0.5;
			if (startMinutes == 45) startHours = parseInt(startHours) + 0.75;
			var endHours = parseInt(data[user].saturdayOut.toString().slice(0, -2));
			var endMinutes = data[user].saturdayOut.toString().slice(-2);
			if (endMinutes == 15) endHours = parseInt(endHours) + 0.25;
			if (endMinutes == 30) endHours = parseInt(endHours) + 0.5;
			if (endMinutes == 45) endHours = parseInt(endHours) + 0.75;
			total += parseFloat(
				endHours - startHours > 6.5 ? endHours - startHours - 0.5 : endHours - startHours
			);
		}
		if (data[user].sundayIn) {
			var startHours = parseInt(data[user].sundayIn.toString().slice(0, -2));
			var startMinutes = data[user].sundayIn.toString().slice(-2);
			if (startMinutes == 15) startHours = parseInt(startHours) + 0.25;
			if (startMinutes == 30) startHours = parseInt(startHours) + 0.5;
			if (startMinutes == 45) startHours = parseInt(startHours) + 0.75;
			var endHours = parseInt(data[user].sundayOut.toString().slice(0, -2));
			var endMinutes = data[user].sundayOut.toString().slice(-2);
			if (endMinutes == 15) endHours = parseInt(endHours) + 0.25;
			if (endMinutes == 30) endHours = parseInt(endHours) + 0.5;
			if (endMinutes == 45) endHours = parseInt(endHours) + 0.75;
			total += parseFloat(
				endHours - startHours > 6.5 ? endHours - startHours - 0.5 : endHours - startHours
			);
		}
		data[user]['hours'] = total;
		var bigTotal = 0;
		data.forEach((element) => {
			bigTotal += element.hours;
		});
		totalHours = bigTotal;

		data.forEach((user) => {
			days.forEach((day) => {
				times.forEach((time) => {
					if (user[day + 'In'] && time >= user[day + 'In'] && time <= user[day + 'Out']) {
						hourTotals.update((val) => [...val, day + time]);
					}
				});
			});
		});

		updated = false;

		setTimeout(function () {
			updated = true;
		}, 100);
	}

	function saveModal() {
		data[showModal].mondayAvailable = document.getElementById('mondayAvailable').value;
		data[showModal].mondayAvailableTo = document.getElementById('mondayAvailableTo').value;
		data[showModal].tuesdayAvailable = document.getElementById('tuesdayAvailable').value;
		data[showModal].tuesdayAvailableTo = document.getElementById('tuesdayAvailableTo').value;
		data[showModal].wednesdayAvailable = document.getElementById('wednesdayAvailable').value;
		data[showModal].wednesdayAvailableTo = document.getElementById('wednesdayAvailableTo').value;
		data[showModal].thursdayAvailable = document.getElementById('thursdayAvailable').value;
		data[showModal].thursdayAvailableTo = document.getElementById('thursdayAvailableTo').value;
		data[showModal].fridayAvailable = document.getElementById('fridayAvailable').value;
		data[showModal].fridayAvailableTo = document.getElementById('fridayAvailableTo').value;
		data[showModal].saturdayAvailable = document.getElementById('saturdayAvailable').value;
		data[showModal].saturdayAvailableTo = document.getElementById('saturdayAvailableTo').value;
		data[showModal].sundayAvailable = document.getElementById('sundayAvailable').value;
		data[showModal].sundayAvailableTo = document.getElementById('sundayAvailableTo').value;

		data[showModal].mondayIn = document.getElementById('mondayIn').value;
		data[showModal].mondayOut = document.getElementById('mondayOut').value;
		data[showModal].tuesdayIn = document.getElementById('tuesdayIn').value;
		data[showModal].tuesdayOut = document.getElementById('tuesdayOut').value;
		data[showModal].wednesdayIn = document.getElementById('wednesdayIn').value;
		data[showModal].wednesdayOut = document.getElementById('wednesdayOut').value;
		data[showModal].thursdayIn = document.getElementById('thursdayIn').value;
		data[showModal].thursdayOut = document.getElementById('thursdayOut').value;
		data[showModal].fridayIn = document.getElementById('fridayIn').value;
		data[showModal].fridayOut = document.getElementById('fridayOut').value;
		data[showModal].saturdayIn = document.getElementById('saturdayIn').value;
		data[showModal].saturdayOut = document.getElementById('saturdayOut').value;
		data[showModal].sundayIn = document.getElementById('sundayIn').value;
		data[showModal].sundayOut = document.getElementById('sundayOut').value;
		calculateHours(showModal);
		showModal = false;
	}

	function addNewEmp() {
		var name = prompt("Enter associate's name...");
		if (name) {
			var oldData = data;
			oldData.push({
				name: name,
				mondayAvailable: false,
				mondayAvailableTo: false,
				tuesdayAvailable: false,
				tuesdayAvailableTo: false,
				wednesdayAvailable: false,
				wednesdayAvailableTo: false,
				thursdayAvailable: false,
				thursdayAvailableTo: false,
				fridayAvailable: false,
				fridayAvailableTo: false,
				saturdayAvailable: false,
				saturdayAvailableTo: false,
				sundayAvailable: false,
				sundayAvailableTo: false,
				mondayIn: false,
				mondayOut: false,
				tuesdayIn: false,
				tuesdayOut: false,
				wednesdayIn: false,
				wednesdayOut: false,
				thursdayIn: false,
				thursdayOut: false,
				fridayIn: false,
				fridayOut: false,
				saturdayIn: false,
				saturdayOut: false,
				sundayIn: false,
				sundayOut: false,
				hours: 0
			});
			data = oldData;
		}
	}
</script>

<div class="summary">
	<div class="h-[8vh]  text-[4vh] flex justify-around">
		<div on:click={load} class="cursor-pointer">📂</div>
		<div on:click={save} class="cursor-pointer">💾</div>
		<div on:click={saveAs} class="cursor-pointer">✏️</div>
		<div class="cursor-pointer">📄</div>
	</div>

	<div
		class="h-[4vh] text-[2vh] font-bold border-orange-500 border-b-4 p-1 pr-3 flex justify-between"
	>
		<div><span on:click={addNewEmp} class="cursor-pointer">👤</span> {file || 'New Schedule'}</div>
		<div>{totalHours}</div>
	</div>
	{#each data as item, i}
		<div
			class="h-[3vh] text-[2vh] font-bold border-orange-500 border-b-2 pl-3 pr-3 flex justify-between"
		>
			<div class="cursor-pointer" on:click={() => editEmp(i)}>{item.name}</div>
			<div>{item.hours}</div>
		</div>
	{/each}
</div>
<div class="scheduler">
	<div class="flex w-[599.76vw]">
		<div class="h-[4vh] w-[85.68vw] text-[2vh] p-1 text-center">
			MONDAY * MONDAY * MONDAY * MONDAY * MONDAY * MONDAY * MONDAY * MONDAY * MONDAY * MONDAY *
			MONDAY * MONDAY * MONDAY * MONDAY * MONDAY
		</div>
		<div class="h-[4vh] w-[85.68vw] text-[2vh] p-1 text-center">
			TUESDAY * TUESDAY * TUESDAY * TUESDAY * TUESDAY * TUESDAY * TUESDAY * TUESDAY * TUESDAY *
			TUESDAY * TUESDAY * TUESDAY * TUESDAY * TUESDAY
		</div>
		<div class="h-[4vh] w-[85.68vw] text-[2vh] p-1 text-center">
			WEDNESDAY * WEDNESDAY * WEDNESDAY * WEDNESDAY * WEDNESDAY * WEDNESDAY * WEDNESDAY * WEDNESDAY
			* WEDNESDAY * WEDNESDAY * WEDNESDAY
		</div>
		<div class="h-[4vh] w-[85.68vw] text-[2vh] p-1 text-center">
			THURSDAY * THURSDAY * THURSDAY * THURSDAY * THURSDAY * THURSDAY * THURSDAY * THURSDAY *
			THURSDAY * THURSDAY * THURSDAY * THURSDAY * THURSDAY
		</div>
		<div class="h-[4vh] w-[85.68vw] text-[2vh] p-1 text-center">
			FRIDAY * FRIDAY * FRIDAY * FRIDAY * FRIDAY * FRIDAY * FRIDAY * FRIDAY * FRIDAY * FRIDAY *
			FRIDAY * FRIDAY * FRIDAY * FRIDAY * FRIDAY
		</div>
		<div class="h-[4vh] w-[85.68vw] text-[2vh] p-1 text-center">
			SATURDAY * SATURDAY * SATURDAY * SATURDAY * SATURDAY * SATURDAY * SATURDAY * SATURDAY *
			SATURDAY * SATURDAY * SATURDAY * SATURDAY * SATURDAY
		</div>
		<div class="h-[4vh] w-[85.68vw] text-[2vh] p-1 text-center">
			SUNDAY * SUNDAY * SUNDAY * SUNDAY * SUNDAY * SUNDAY * SUNDAY * SUNDAY * SUNDAY * SUNDAY *
			SUNDAY * SUNDAY * SUNDAY * SUNDAY * SUNDAY
		</div>
	</div>

	<div class="h-[4vh] w-[599.76vw] text-[1vw] font-bold text-orange-600 flex items-center">
		{#if updated}
			{#each days as day}
				{#each times as time}
					<div class="h-full w-[1.19vw] text-center">
						{$hourTotals.filter((x) => x == day + time).length}
					</div>
				{/each}
			{/each}
		{/if}
	</div>

	<div
		class="h-[4vh] w-[599.76vw] text-[2vh] font-bold border-orange-500 border-b-4 p-1 flex items-center"
	>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">5</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">6</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">7</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">8</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">9</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">10</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">11</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">12</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">1</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">2</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">3</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">4</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">5</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">6</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">7</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">8</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">9</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">10</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200" />
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">5</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">6</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">7</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">8</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">9</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">10</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">11</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">12</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">1</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">2</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">3</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">4</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">5</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">6</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">7</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">8</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">9</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">10</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200" />
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">5</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">6</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">7</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">8</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">9</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">10</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">11</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">12</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">1</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">2</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">3</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">4</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">5</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">6</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">7</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">8</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">9</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">10</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200" />
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">5</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">6</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">7</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">8</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">9</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">10</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">11</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">12</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">1</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">2</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">3</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">4</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">5</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">6</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">7</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">8</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">9</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">10</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200" />
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">5</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">6</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">7</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">8</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">9</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">10</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">11</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">12</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">1</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">2</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">3</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">4</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">5</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">6</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">7</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">8</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">9</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">10</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200" />
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">5</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">6</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">7</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">8</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">9</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">10</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">11</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">12</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">1</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">2</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">3</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">4</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">5</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">6</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">7</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">8</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">9</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">10</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200" />
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">5</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">6</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">7</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">8</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">9</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">10</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">11</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">12</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">1</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">2</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">3</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">4</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">5</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">6</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">7</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">8</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">9</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">45</div>
		<div class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200">10</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">15</div>
		<div class="h-full w-[1.19vw] text-[.5vw] border-r-2 border-orange-200">30</div>
	</div>
	{#each data as item, i}
		<div
			class="h-[3vh] w-[599.76vw] text-[2vh] font-bold border-orange-500 border-b-2 p-1 flex items-center"
		>
			{#each days as day}
				{#each times as time}
					{#if item[day + 'In'] && time >= item[day + 'In'] && time < item[day + 'Out']}
						<div
							on:contextmenu|preventDefault={() => handleRightClick(i, day)}
							on:click={() => handleClick(i, day, time)}
							class="h-full w-[1.19vw] text-[1vw] border-r-2 bg-green-400 border-orange-200"
						/>
					{:else if item[day + 'Available'] && time >= item[day + 'Available'] && time + 15 <= item[day + 'AvailableTo']}
						<div
							on:contextmenu|preventDefault={() => handleRightClick(i, day)}
							on:click={() => handleClick(i, day, time)}
							class="h-full w-[1.19vw] text-[1vw] border-r-2 bg-green-200 border-orange-200"
						/>
					{:else}
						<div
							on:contextmenu|preventDefault={() => handleRightClick(i, day)}
							on:click={() => handleClick(i, day, time)}
							class="h-full w-[1.19vw] text-[1vw] border-r-2 border-orange-200"
						/>
					{/if}
				{/each}
			{/each}
		</div>
	{/each}
</div>
{#if showModal !== false}
	<div class="modal">
		<button on:click={saveModal} class="m-3 p-3 text-xl bg-green-400">Save</button>
		<button on:click={() => (showModal = false)} class="m-3 p-3 text-xl bg-red-400">Cancel</button>
		<div class="text-xl font-bold m-3">{data[showModal].name}</div>
		<div class="text-xl font-bold m-3">Availibility</div>
		<input
			id="mondayAvailable"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].mondayAvailable || ''}
		/>
		<input
			id="mondayAvailableTo"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].mondayAvailableTo || ''}
		/>
		Monday<br />
		<input
			id="tuesdayAvailable"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].tuesdayAvailable || ''}
		/>
		<input
			id="tuesdayAvailableTo"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].tuesdayAvailableTo || ''}
		/>
		Tuesday<br />
		<input
			id="wednesdayAvailable"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].wednesdayAvailable || ''}
		/>
		<input
			id="wednesdayAvailableTo"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].wednesdayAvailableTo || ''}
		/>
		Wednesday<br />
		<input
			id="thursdayAvailable"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].thursdayAvailable || ''}
		/>
		<input
			id="thursdayAvailableTo"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].thursdayAvailableTo || ''}
		/>
		Thursday<br />
		<input
			id="fridayAvailable"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].fridayAvailable || ''}
		/>
		<input
			id="fridayAvailableTo"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].fridayAvailableTo || ''}
		/>
		Friday<br />
		<input
			id="saturdayAvailable"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].saturdayAvailable || ''}
		/>
		<input
			id="saturdayAvailableTo"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].saturdayAvailableTo || ''}
		/>
		Saturday<br />
		<input
			id="sundayAvailable"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].sundayAvailable || ''}
		/>
		<input
			id="sundayAvailableTo"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].sundayAvailableTo || ''}
		/>
		Sunday<br />

		<div class="text-xl font-bold m-3">Schedule</div>
		<input
			id="mondayIn"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].mondayIn || ''}
		/>
		<input
			id="mondayOut"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].mondayOut || ''}
		/>
		Monday<br />
		<input
			id="tuesdayIn"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].tuesdayIn || ''}
		/>
		<input
			id="tuesdayOut"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].tuesdayOut || ''}
		/>
		Tuesday<br />
		<input
			id="wednesdayIn"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].wednesdayIn || ''}
		/>
		<input
			id="wednesdayOut"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].wednesdayOut || ''}
		/>
		Wednesday<br />
		<input
			id="thursdayIn"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].thursdayIn || ''}
		/>
		<input
			id="thursdayOut"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].thursdayOut || ''}
		/>
		Thursday<br />
		<input
			id="fridayIn"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].fridayIn || ''}
		/>
		<input
			id="fridayOut"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].fridayOut || ''}
		/>
		Friday<br />
		<input
			id="saturdayIn"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].saturdayIn || ''}
		/>
		<input
			id="saturdayOut"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].saturdayOut || ''}
		/>
		Saturday<br />
		<input
			id="sundayIn"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].sundayIn || ''}
		/>
		<input
			id="sundayOut"
			class="px-3 mx-3 mb-1"
			type="number"
			min="500"
			max="2300"
			value={data[showModal].sundayOut || ''}
		/>
		Sunday<br />
	</div>
{/if}

<style>
	.modal {
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background-color: tan;
	}
	.summary {
		position: absolute;
		top: 0;
		left: 0px;
		width: 15vw;
		bottom: 0px;
		background-color: rgb(248, 228, 201);
	}
	.scheduler {
		position: absolute;
		top: 0;
		left: 15vw;
		width: 85vw;
		bottom: 0px;
		overflow: auto;
		background-color: rgb(248, 238, 225);
	}
</style>
