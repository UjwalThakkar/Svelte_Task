<script lang="ts">
	import {
		Dialog,
		DialogTrigger,
		DialogContent,
		DialogHeader,
		DialogTitle,
		DialogFooter
	} from '$lib/components/ui/dialog';
	import { Button } from '$lib/components/ui/button';
	import { Input } from '$lib/components/ui/input';

	let file = $state(null);
	let student = $state({ name: '', phoneNo: '', email: '' });
	let students = $state([]);

	const downloadTemplate = () => {
		const csvContent = 'name,phoneNo,email\nJohn Doe,9014285420,john@example.com';
		const blob = new Blob([csvContent], { type: 'text/csv' });
		const url = URL.createObjectURL(blob);
		const link = document.createElement('a');
		link.href = url;
		link.download = 'student-template.csv';
		link.click();
		URL.revokeObjectURL(url);
	};

	const handleUpload = (e) => {
		file = e.target.files[0];
		if (file) {
			const reader = new FileReader();
			reader.onload = (event) => {
				const csvData = event.target?.result;
				const parsedStudents = csvData
					?.split('\n')
					.slice(1)
					.map((line) => {
						const [name, phoneNo, email] = line.split(',');
						return { name, phoneNo, email };
					})
					.filter((s) => s.name && s.email);
				students = [...students, ...parsedStudents];
				console.log('File uploaded', $state.snapshot(students));
			};
			reader.readAsText(file);
		}
	};

	const handleSubmit = () => {
		if (!student.name || !student.email) {
			console.log('please enter all details');
			return;
		} else {
			students = [...students, { ...student }];
			console.log('Manual Entry Submited: ', $state.snapshot(students));
			student = { name: '', phoneNo: '', email: '' }; // reset the form
		}
	};
</script>

<div class="container mx-auto p-4">
	<Dialog>
		<h1 class="mb-4 text-2xl font-bold">Student Management</h1>
		<DialogTrigger>
			<Button class="rounded-md text-white ">Add Students</Button>
		</DialogTrigger>
		<DialogContent class="max-w-lg rounded-lg bg-white shadow-lg">
			<DialogHeader>
				<DialogTitle class="text-center text-xl font-bold">Add Students</DialogTitle>
			</DialogHeader>
			<hr />
			<div class="flex flex-col space-y-6">
				<!-- Bulk Addition Section  -->
				<div class="rounded-xl bg-gray-100 p-4">
					<h3 class="text-sm font-semibold text-orange-500">For Bulk Addition:</h3>
					<p class="text-sm text-gray-400">
						Upload your completed Excel template to add multiple students at once.
					</p>
					<div class="mt-2">
						<label for="excel-file" class="text-gray-600">
							Submit Excel File (Use Template) <span class="text-red-500">*</span>
						</label>
						<div class="mt-1 flex items-center gap-4">
							<Input
								id="excel-file"
								type="file"
								class="hidden w-full rounded-md bg-gray-100"
								accept=".xlsx, .xls, .csv"
								name="excel-file"
								placeholder="Attach file"
								onchange={handleUpload}
							/>
							<button
								class="w-[50%] cursor-pointer rounded-xl border border-dashed border-gray-400 bg-white p-2 text-center text-gray-500"
								onclick={() => document.getElementById('excel-file')?.click()}>Attact File</button
							>
							<Button variant="custom" class="bg-white hover:bg-gray-100" onclick={downloadTemplate}
								>Download Template</Button
							>
						</div>
					</div>
				</div>
				<div class="my-4 text-center font-bold uppercase text-orange-500">OR</div>
				<!-- Manual Addition Section  -->
				<div class="rounded-xl bg-gray-100 p-4">
					<h3 class="text-sm font-semibold text-orange-500">For Manual Addition:</h3>
					<p class="text-sm text-gray-400">Enter individual student details one by one.</p>
					<div class="mt-2 space-y-4">
						<div class="flex gap-4">
							<div class="w-[50%]">
								<label for="student-name" class="text-gray-500">
									Student Name <span class="text-red-500">*</span>
								</label>
								<Input
									id="student-name"
									type="email"
									placeholder="Enter Student Name"
									class="w-full rounded-md bg-white"
									bind:value={student.name}
								/>
							</div>
							<div class="w-[50%]">
								<label for="phone-number" class="text-gray-500">
									Phone Number <span class="text-red-500">*</span>
								</label>
								<Input
									id="phone-number"
									type="number"
									placeholder="Enter Phone Number"
									class="w-full rounded-md bg-white"
									bind:value={student.phoneNo}
								/>
							</div>
						</div>
						<div>
							<label for="email-id" class="text-gray-500">
								Email ID <span class="text-red-500">*</span>
							</label>
							<Input
								id="email-id"
								placeholder="Enter Email ID"
								class="w-full rounded-md bg-white"
								bind:value={student.email}
							/>
						</div>
					</div>
				</div>
			</div>
			<DialogFooter>
				<Button variant="custom" class=" border-gray-500 text-gray-700">Cancel</Button>
				<Button variant="custom" class=" bg-[#022f49] text-white" onclick={handleSubmit}
					>Confirm</Button
				>
			</DialogFooter>
		</DialogContent>
	</Dialog>
</div>
