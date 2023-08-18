<template>
  <div>
    <h1>Order Form</h1>
    <div v-for="(cartItem, index) of cartItemsAdded" :key="cartItem.cartItemId">
        <h2>{{ cartItem.name }}</h2>
        <p>Description: {{ cartItem.description }}</p>
        <p>Price: ${{ cartItem.price }}</p>
        <br/>
        <buttom type="button" @click="removeCartItem(index)">
        Remove From Cart
        </buttom>
    </div>
    <Form :validationSchema="schema" @submit="submitOrder">
        <div>
            <label for="name">Name</label>
            <br/>
            <Field name="name" type="text" placeholder="Name"/>
            <ErrorMessage name="name"/>
        </div>
        <br/>
        <div>
            <label for="address">Address</label>
            <br/>
            <Field name="address" type="text" placeholder="Address"/>
            <ErrorMessage name="address"/>
        </div>
        <br/>
        <input type="submit"/>
    </Form>
  </div>
</template>

<script>
export default {
    name:"OrderForm",
    data(){
        return{
            schema,
        };
    },
    components: {
        Form,
        Field,
        ErrorMessage,
    },
    computed:{
        ...mapGetters(["cartItemsAdded"]),
    },
    methods:{
        async submitOrder({ name, phone, address }){
            const mutation = gql`
                mutation addOrder(
                    $name: String
                    $phone: String
                    $address: String
                    $ordered_items: [ShopItem]
                ) {
                    addOrder(
                        order: {
                            name: $name
                            phone: $phone
                            address: $address
                            ordered_items: $ordered_items
                        }
                    ){
                        status
                    }
                }
            `;
            const variables = {
                name,
                phone,
                address,
                ordered_items: this.cartItemsAdded.map(
                    ({ shop_item_id, name, description, image_url, price }) => ({
                        shop_item_id,
                        name,
                        description,
                        image_url,
                        price,
                    })
                ),
            };
            await graphQLClient.request(mutation, variables);
            this.clearCart();
            this.$router.push("/success");
        },
        ...mapMutations(["addCartItem", "removeCartItem", "clearCart"])
    },
};
</script>

<style>

</style>