<template>
  <v-data-table
    :headers="headers"
    :items="productos"
    sort-by="regular_price"
    class="elevation-1"
  >
    <template v-slot:top>
      <v-toolbar
        flat
      >
        <v-toolbar-title>Mi WooCommerce</v-toolbar-title>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
        <v-spacer></v-spacer>
        <v-dialog
          v-model="dialog"
          max-width="500px"
        >
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              color="primary"
              dark
              class="mb-2"
              v-bind="attrs"
              v-on="on"
            >
              Nuevo producto
            </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="headline">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="editedItem.name"
                      label="Título"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="editedItem.short_description"
                      label="Descripción corta"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="editedItem.regular_price"
                      label="Precio"
                    ></v-text-field>
                  </v-col>
                  <!-- <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="editedItem.status"
                      label="Estado"
                    ></v-text-field>
                  </v-col> -->
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="editedItem.id"
                      label="ID"
                    ></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn
                color="blue darken-1"
                text
                @click="close"
              >
                Cancel
              </v-btn>
              <v-btn
                color="blue darken-1"
                text
                @click="save"
              >
                Save
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-dialog v-model="dialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="headline">Are you sure you want to delete this item?</v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="closeDelete">Cancel</v-btn>
              <v-btn color="blue darken-1" text @click="deleteItemConfirm">OK</v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:item.actions="{ item }">
      <v-icon
        small
        class="mr-2"
        @click="editItem(item)"
      >
        mdi-pencil
      </v-icon>
      <v-icon
        small
        @click="deleteItem(item)"
      >
        mdi-delete
      </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn
        color="primary"
        @click="initialize"
      >
        Reset
      </v-btn>
    </template>
  </v-data-table>
</template>
<script>
  export default {
    data: () => ({
      dialog: false,
      dialogDelete: false,
      headers: [
        { text: 'ID', value: 'id' },
        {
          text: 'Título de producto',
          align: 'start',
          sortable: false,
          value: 'name',
        },
        { text: 'Descripción corta', value: 'short_description' },
        { text: 'Precio', value: 'regular_price' },
        { text: 'Estado', value: 'status' },
        { text: 'Acción', value: 'actions', sortable: false },
      ],
      productos: [],
      editedIndex: -1,
      editedItem: {
        name: '',
        regular_price: 0,
        // status: '',
        short_description: '',
        id: 0,
      },
      defaultItem: {
        name: '',
        regular_price: 0,
        status: 0,
        short_description: '',
        id: 0,
      },
    }),

    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'Nuevo producto' : 'Editar producto'
      },
    },

    watch: {
      dialog (val) {
        val || this.close()
      },
      dialogDelete (val) {
        val || this.closeDelete()
      },
    },

    created () {
      this.initialize()
    },

    methods: {
      initialize () {

          const WooCommerceRestApi = require("@woocommerce/woocommerce-rest-api").default;
          const api = new WooCommerceRestApi({
            url: "https://nuevo-eshopping.com/",
            consumerKey: "ck_39216abb4abf27b0177ae5ec431ed900c10f637b",
            consumerSecret: "cs_094b361782eb9d1a6189b8d01007df375a95e1ef",
            version: "wc/v3"
          });

          // List products
          api.get("products", {
            per_page: 20, // 20 products per page
          })
            .then((response) => {
              // Successful request
              this.productos = response.data
            })

      },

      editItem (item) {
        this.editedIndex = this.productos.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialog = true
      },

      deleteItem (item) {
        this.editedIndex = this.productos.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialogDelete = true


      },

      deleteItemConfirm () {

        this.productos.splice(this.editedIndex, 1)

        if(this.productos.splice(this.editedIndex, 1)){
          
          this.editedIndex, 1

          const WooCommerceRestApi = require("@woocommerce/woocommerce-rest-api").default;
          const api = new WooCommerceRestApi({
            url: "https://nuevo-eshopping.com/",
            consumerKey: "ck_39216abb4abf27b0177ae5ec431ed900c10f637b",
            consumerSecret: "cs_094b361782eb9d1a6189b8d01007df375a95e1ef",
            version: "wc/v3"
          });

          // Delete a product
            api.delete("products/"+this.editedIndex, {
              force: false, // Forces to delete instead of move to the Trash
            })
              .then((response) => {
                console.log("Response Data:", response.data);
              })
              .catch((error) => {
                console.log("Response Data:", error.response.data);
              })

        }



        this.closeDelete()
      },

      close () {
        this.dialog = false
        this.$nextTick(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        })
      },

      closeDelete () {
        this.dialogDelete = false
        this.$nextTick(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        })
      },

      save () {
        if (this.editedIndex > -1) {


          Object.assign(this.productos[this.editedIndex], this.editedItem)
          // editar

          const WooCommerceRestApi = require("@woocommerce/woocommerce-rest-api").default;
          const api = new WooCommerceRestApi({
            url: "https://nuevo-eshopping.com/",
            consumerKey: "ck_39216abb4abf27b0177ae5ec431ed900c10f637b",
            consumerSecret: "cs_094b361782eb9d1a6189b8d01007df375a95e1ef",
            version: "wc/v3"
          });

          // Edit a product
          api.put("products/"+this.editedItem.id, {
              name: this.editedItem.name,
              short_description: this.editedItem.short_description,
              regular_price: this.editedItem.regular_price
          })
            .then((response) => {

              console.log("Response Data:", response.data);
            })
            .catch((error) => {
              console.log("Response Data:", error.response.data);
            })

        } else {

          // Create a product
          this.productos.push(this.editedItem)

          const WooCommerceRestApi = require("@woocommerce/woocommerce-rest-api").default;
          const api = new WooCommerceRestApi({
            url: "https://nuevo-eshopping.com/",
            consumerKey: "ck_39216abb4abf27b0177ae5ec431ed900c10f637b",
            consumerSecret: "cs_094b361782eb9d1a6189b8d01007df375a95e1ef",
            version: "wc/v3"
          });

            api.post("products", {
              
              name: this.editedItem.name,
              short_description: this.editedItem.short_description,
              regular_price: this.editedItem.regular_price
            })
              .then((response) => {
                console.log("Response Data:", response.data);
              })
              .catch((error) => {
                console.log("Response Data:", error.response.data);
              })

        }
        this.close()
      },
    },
  }
</script>