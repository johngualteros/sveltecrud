<script>
	import {v4} from 'uuid';
	import Noty from 'noty';
	import 'noty/lib/noty.css';
	import "noty/lib/themes/sunset.css";
	
	import { writable } from 'svelte-local-storage-store';
	import { get } from 'svelte/store';

	const preferences = writable('preferences', {
	});
	preferences.products = writable('products', {
		get(preferences) {
			return preferences.products;
		},
		$preferences
	});

	let products=[
		{
			id:1,
			name:"Hp pavilon laptop",
			description:"blah bla",
			category:"laptop",
		},
		{
			id:2,
			name:"Razer Mouse",
			description:"Gaming Mouse",
			category:"peripherials",
		}
	];
	let product={
		id:'',
		name:'',
		description:'',
		category:'',
		imageURL:''
	};
	let editStatus=false;
	const addProduct=()=>{
		const newProduct={
			...product,
			id:v4(),
		};
		
		products=products.concat(newProduct);
		cleanProduct();
	}
	const updateProduct=()=>{
		let updatedProduct={
			name:product.name,
			description:product.description,
			id:product.id,
			category:product.category,
			imageURL:product.imageURL
		};
		const productIndex=products.findIndex(p=>p.id===product.id);
		products[productIndex]=updatedProduct;
		cleanProduct();
		editStatus=false;
		new Noty({
			text:'Product Updated',
			theme:'sunset',
			type:'success',
			layout:'topRight',
			timeout:2000
		}).show();
	}
	const cleanProduct=()=>{
		product={
			id:'',
			name:'',
			description:'',
			category:'',
			imageURL:''
		};
	};
	const onSubmitHandler=()=>{
		if (!editStatus){
			addProduct();
		}else{
			updateProduct();
		}
	}
	const handleRemove=(id)=>{
		products=products.filter(product=>product.id!==id);
	}
	const handleEdit=(productEdited)=>{
		product=productEdited;
		editStatus=true;
	}
	const load=()=>{
		console.log('App loaded');
	}
</script>

<main>
	<div class="container pt-5">
		<div class="row">
			<div class="col-md-6">
				{#each products as product}
				<div class="card mb-3 border-light">
					<div class="row">
						<div class="col-md-4">
							{#if !product.imageURL}
							<img src="images/No_Product_Found.png" class="card-img-top" alt="{product.name}" />
							{:else}
							<img src="{product.imageURL}" class="card-img-top" alt="{product.name}" />
							{/if}
						</div>
						<div class="col-md-8">
							<div class="card-body">
								<h5 class="card-title">{product.name} <span class="text-secondary"> {product.category}</span></h5>
								<p class="card-text">{product.description}</p>
								<div class="row">
									<div class="col-md-6">
										<button on:click={handleEdit(product)} class="btn btn-primary w-100">
											Edit
										</button>
									</div>
									<div class="col-md-6">
										<buttton on:click={handleRemove(product.id)} class="btn btn-danger w-100">
											Delete
										</buttton>
									</div>
								</div>
							</div>
						</div>
					</div>
				</div>
				{/each}
			</div>
			<div class="col-md-6">
				<div class="card border-secondary">
					<div class="card-body">
						<form on:submit|preventDefault={onSubmitHandler}>
							<div class="form-group mb-3">
								<input type="text" placeholder="Product Name" id="product_name" class="form-control" bind:value={product.name}>
							</div>
							<div class="form-group mb-3">
								<textarea id="product_description" rows="3" placeholder="Product Description" class="form-control" bind:value={product.description}/>
							</div>
							<div class="form-group mb-3">
								<input type="url" id="product_image_url" placeholder="https://www.google.com" class="form-control" bind:value={product.imageURL}>
							</div>
							<div class="form-group mb-3">
								<select id="category" class="form-select" bind:value={product.category}>
									<option active value="laptops">Laptops</option>
									<option value="peripherials">Pheripherials</option>
									<option value="servers">Servers</option>
								</select>
							</div>
							<button type="submit" class="btn w-100 rounded btn-outline-light">
								{#if editStatus==true}
									Update Product
								{:else}
									Save Product
								{/if}
							</button>
						</form>
					</div>
				</div>
			</div>
		</div>
	</div>
</main>

<style>
	main{
		width: 100%;
		height: auto;
		min-height: 100vh;
	}
</style>