<script>
	import { emailValidator, requiredValidator } from "./validators.js";
	import { createFieldValidator } from "./validation.js";

	const [validity, validate] = createFieldValidator(
		requiredValidator(),
		emailValidator()
	);

	let ip = "";
	let disp_ip = "";
	let country = "";
	let region = "";
	let isp = "";
	let lat = "";
	let lon = "";

	const getData = () => {
		output.setAttribute("hidden", "");
		fetch("/api/" + ip)
			.then((res) => res.json())
			.then((data) => {
				disp_ip = ip;
				country = data.country;
				region = data.regionName;
				isp = data.isp;
				lat = data.lat;
				lon = data.lon;
				output.removeAttribute("hidden");
			});
	};
</script>

<style>
	.container {
		margin-top: 50px;
		min-height: 100%;
		padding: 0;
		margin-left: 10%;
		max-width: 80%;
	}
	#ip-add {
		max-width: 70%;
	}

	#map {
		width: 100%;
		height: 300px;
		overflow: auto;
	}
	input {
		outline: none;
		border-width: 2px;
	}

	.validation-hint {
		color: red;
		padding: 6px 0;
	}

	.field-danger {
		border-color: red;
	}

	.field-success {
		border-color: green;
	}
</style>

<div class="container">
	<center>
		<h2>IP Geolocator</h2>
		<input
			type="text"
			placeholder="Enter IP address"
			bind:value={ip}
			id="ip-add"
			class:field-danger={!$validity.valid}
			class:field-success={$validity.valid}
			use:validate={ip} />
		<button
			disabled={!$validity.valid}
			type="button"
			class="btn btn-success"
			on:click={getData}>Locate IP</button>
		<div hidden id="output">
			<div class="card">
				IP:
				<b>{disp_ip}</b>
				Country:
				<b>{country}</b>
				&nbsp; Region:
				<b>{region}</b>
				&nbsp; ISP:
				<b>{isp}</b>
			</div><br /><br />
			<iframe
				id="map"
				title="map"
				frameborder="0"
				scrolling="no"
				marginheight="0"
				marginwidth="0"
				src="https://www.openstreetmap.org/export/embed.html?bbox={lon - 0.5}%2C{lat - 0.5}%2C{lon + 0.5}%2C{lat + 0.5}&layer=mapnik▮={lat}%2C{lon}"
				style="border: 1px solid black" /><br />
		</div>
	</center>
</div>
