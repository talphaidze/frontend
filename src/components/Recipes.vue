<template>
<div>
    <div class="jumbotron vertical-center">
      <div class="container">
        <div class="row">
          <div class="col-sm-12">
            <h1>Recipes</h1>
            <hr />
            <br />
            <!-- Allert Message -->
            <b-alert v-if=showMessage variant="success" show>{{ message }}</b-alert>
            <button type="button" class="btn btn-success btn-sm" v-b-modal.recipe-modal>
              Add Recipe
            </button>
            <br /><br />
            <table class="table table-hover">
              <thead>
                <tr>
                  <th scope="col">Recipe Name</th>
                  <th scope="col">Ingredients</th>
                  <th scope="col">Rating</th>
                  <th scope="col">Favorite</th>
                  <th scope="col">Actions</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="recipe in recipes" :key="recipe.id">
                <td>{{ recipe.name }}</td>
                <td>{{ recipe.ingredients }}</td>
                <td>
                <p v-if="recipe.rating === '5'">😁 - ⭐⭐⭐⭐⭐</p>
                <p v-else-if="recipe.rating === '4'">😊 - ⭐⭐⭐⭐</p>
                <p v-else-if="recipe.rating === '3'">😐 - ⭐⭐⭐</p>
                <p v-else-if="recipe.rating === '2'">😞 - ⭐⭐</p>
                <p v-else-if="recipe.rating === '1'">😭 - ⭐</p>
                </td>
                <td>
                <label v-bind:for="recipe.id">
                <input
                type="checkbox"
                :checked="Yes"
                @input="checked = $event.target.checked"
                />
                </label>
                </td>


                    <div class="btn-group" role="group">
                      <button type="button" class="btn btn-info btn-sm" v-b-modal.edit-recipe-modal @click="updateRecipe(recipe)">
                        Edit
                      </button>
                      <button type="button" class="btn btn-danger btn-sm"  @click="deleteRecipe(recipe)">
                        Delete
                      </button>
                    </div>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>

     <!-- Start of Modal for Create Recipe-->
      <b-modal
        ref="addRecipeModal"
        id="recipe-modal"
        title="Create a new recipe"
        hide-backdrop
        hide-footer
      >
        <b-form @submit="onSubmit" class="w-100">
          <b-form-group
            id="form-name-group"
            label="Recipe Name"
            label-for="form-name-input"
          >
            <b-form-input
              id="form-name-input"
              type="text"
              v-model="createRecipeForm.name"
              placeholder="Recipe Name"
              required
            >
            </b-form-input>
          </b-form-group>
          <b-form-group
            id="form-ingredients-list"
            label="Ingredients"
            label-for="form-ingredients-list"
          >
            <b-form-input
              id="form-ingredients-list"
              type="text"
              v-model="createRecipeForm.ingredients"
              placeholder="Ingredients"
              required
            >
            </b-form-input>
          </b-form-group>
          <b-form-group
            id="form-rating-list"
            label="Rating"
            label-for="form-rating-list"
          >
            <b-form-input
              id="form-rating-list"
              type="text"
              v-model="createRecipeForm.rating"
              placeholder="1 to 5"
              required
            >
            </b-form-input>
          </b-form-group>
          <b-form-group
            id="form-favorite-list"
            label="Favorite"
            label-for="form-favorite-list"
          >
            <b-form-input
              id="form-favorite-list"
              type="text"
              v-model="createRecipeForm.favorite"
              placeholder="Yes or No"
              required
            >
            
            

            </b-form-input>
          </b-form-group>

          <b-button type="submit" variant="outline-info">Submit</b-button>
        </b-form>
      </b-modal>
      <!-- End of Modal for Create Recipe-->

    <!-- Start of Modal for Edit Recipe-->
      <b-modal
        ref="editRecipeModal"
        id="edit-recipe-modal"
        title="Edit the recipe"
        hide-backdrop
        hide-footer
      >
        <b-form @submit="onSubmitUpdate" class="w-100">
          <b-form-group
            id="form-edit-name-group"
            label="Recipe Name:"
            label-for="form-edit-name-input"
          >
            <b-form-input
              id="form-edit-name-input"
              type="text"
              v-model="editRecipeForm.name"
              placeholder="Recipe Name"
              required
            >
            </b-form-input>
          </b-form-group>
          <b-form-group
            id="form-ingredients-list"
            label="Ingredients"
            label-for="form-ingredients-list"
          >
            <b-form-input
              id="form-ingredients-list"
              type="text"
              v-model="editRecipeForm.ingredients"
              placeholder="Ingredients"
              required
            >
            </b-form-input>
          </b-form-group>
          <b-form-group
            id="form-rating-list"
            label="Rating"
            label-for="form-rating-list"
          >
            <b-form-input
              id="form-rating-list"
              type="text"
              v-model="editRecipeForm.rating"
              placeholder="1 to 5"
              required
            >
            </b-form-input>
          </b-form-group>
          <b-form-group
            id="form-favorite-list"
            label="Favorite"
            label-for="form-favorite-list"
          >
            <b-form-input
              id="form-favorite-list"
              type="text"
              v-model="editRecipeForm.favorite"
              placeholder="Yes or No"
              required
            >
            </b-form-input>
          </b-form-group>
          <b-button type="submit" variant="outline-info">Submit</b-button>
        </b-form>
      </b-modal>
      <!-- End of Modal for Edit Recipe-->

</div>

  </template>

 
<script>
import axios from "axios";
export default {
  name: "Recipes",
  data() {
    return {
      recipes: [],
        createRecipeForm: {
        name: "",
        ingredients: "",
        rating: "",
        favorite: "",
      },
        editRecipeForm: {
        id: "",
        name: "",
        ingredients: "",
        rating: "",
        favorite: "",
      },

      showMessage: false,

    };
  },
  methods: {
    // GET function
    getRecipes() {
      const path = `${process.env.VUE_APP_ROOT_URL}/recipes`;
      axios
        .get(path)
        .then((response) => {
          this.recipes = response.data.recipes;
        })
        .catch((error) => {
          console.error(error);
        });
    },
    // POST function
    createRecipe(payload) {
      const path = `${process.env.VUE_APP_ROOT_URL}/recipes`;
      axios
        .post(path, payload)
        .then((response) => {
          this.getRecipes();
          // For message alert
            this.message = "Recipe Added succesfully!";
            // To actually show the message
            this.showMessage = true;
            // To hide the message after 3 seconds
            setTimeout(() => {
              this.showMessage = false;
            }, 3000);

        })
        .catch((error) => {
          console.error(error);
          this.getRecipes();
        });
    },
    // Update function
    updateRecipe(payload, recipeId) {
      const path = `${process.env.VUE_APP_ROOT_URL}/recipes/${recipeId}`;
      axios
        .put(path, payload)
        .then((response) => {
          this.getRecipes();
          // For message alert
          this.message = "Recipe Updated succesfully!";
          // To actually show the message
          this.showMessage = true;
          // To hide the message after 3 seconds
          setTimeout(() => {
            this.showMessage = false;
          }, 3000);
        })
        .catch((error) => {
          console.error(error);
          this.getRecipes();
        });
    },
    // Delete recipe
    RESTdeleteRecipe(recipeId) {
      const path = `${process.env.VUE_APP_ROOT_URL}/recipes/${recipeId}`;
      axios
        .delete(path)
        .then((response) => {
          this.RESTgetRecipes();
          // For message alert
          this.message = "Recipe Deleted succesfully!";
          // To actually show the message
          this.showMessage = true;
          // To hide the message after 3 seconds
          setTimeout(() => {
            this.showMessage = false;
          }, 3000);
        })
        .catch((error) => {
          console.error(error);
          this.RESTgetRecipes();
        });
    },
    // Handle Delete button
    deleteRecipe(recipe) {
      this.RESTdeleteRecipe(recipe.id);
    },
 


    initForm() {
      this.createRecipeForm.name = "";
      this.createRecipeForm.ingredients = "";
      this.createRecipeForm.rating = "";
      this.createRecipeForm.favorite = "";
      this.editRecipeForm.id = "";
      this.editRecipeForm.name = "";
      this.editRecipeForm.ingredients = "";
      this.editRecipeForm.rating = "";
      this.editRecipeForm.favorite = "";
    },
    onSubmit(e) {
      e.preventDefault(); //prevent default form submit form the browser
      this.$refs.addRecipeModal.hide(); //hide the modal when submitted
      const payload = {
        name: this.createRecipeForm.name,
        ingredients: this.createRecipeForm.ingredients,
        rating: this.createRecipeForm.rating,
        favorite: this.createRecipeForm.favorite,
      };
      this.createRecipe(payload);
      this.initForm;
    },
    // Handle submit event for edit recipe
    onSubmitUpdate(e) {
      e.preventDefault(); //prevent default form submit form the browser
      this.$refs.editRecipeModal.hide(); //hide the modal when submitted
      const payload = {
        name: this.editRecipeForm.name,
        ingredients: this.editRecipeForm.ingredients,
        rating: this.editRecipeForm.rating,
        favorite: this.editRecipeForm.favorite,
      };
      this.updateRecipe(payload, this.editRecipeForm.id);
    },
 
 
    //Prepare edit recipe form with the name of the recipe
    editRecipe(recipe) {
      this.editRecipeForm.name = recipe.name;
      this.editRecipeForm.id = recipe.id;
      this.editRecipeForm.ingredients = recipe.ingredients;
      this.editRecipeForm.rating = recipe.rating;
      this.editRecipeForm.favorite = recipe.favorite;
    },
 


  },
  created() {
    this.getRecipes();
  },
};
</script>
