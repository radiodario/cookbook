<script setup>
import { useShoppingListStore } from "../stores/shopping-list.js";

const store = useShoppingListStore();

function check(recipeTitle, ingredientIndex, done) {
  store.updateIngredientDone(recipeTitle, ingredientIndex, done);
}

function removeFromList(recipeTitle) {
  store.removeFromList(recipeTitle);
}

const searchTerm = ref("");

function filter(ingredients = []) {
  return ingredients.filter((item) =>
    item.name.toLowerCase().includes(searchTerm.value.toLowerCase())
  );
}
</script>

<template>
  <ClientOnly>
    <r-grid columns="8">
      <r-cell span="row">
        <h3><NuxtLink to="/">↩ Home</NuxtLink></h3>
      </r-cell>
      <r-cell span="row">
        <h1>List</h1>
        <input
          aria-label="Search"
          type="search"
          v-model="searchTerm"
          v-if="store.list.length"
        />
      </r-cell>
    </r-grid>

    <r-grid columns="8" v-for="item in store.list">
      <r-cell span="1-6" span-s="row">
        <h2>
          <NuxtLink :to="`/recipe/${item.title}`"> {{ item.title }} →</NuxtLink>
        </h2>
      </r-cell>
      <r-cell span="7-8" span-s="row" class="list-button-container">
        <button @click="removeFromList(item.title)">
          <h3>Remove from List</h3>
        </button>
      </r-cell>
      <r-cell span="1-6" span-s="row">
        <fieldset>
          <label
            v-for="ingredient in filter(item.ingredients)"
            :class="{ checked: ingredient.done }"
          >
            <r-grid columns="8">
              <r-cell span="1-4" class="checkbox-container">
                <div>
                  <input
                    type="checkbox"
                    :id="`${item.title}-${ingredient.name}`"
                    :checked="ingredient.done"
                    @change="
                      check(item.title, ingredient.index, !ingredient.done)
                    "
                  />
                </div>
                <div>
                  <span class="name">{{ ingredient.name }}</span>
                </div>
              </r-cell>
              <r-cell span="5-8">
                <span class="quantity">{{ ingredient.quantity }}</span>
              </r-cell>
            </r-grid>
          </label>
        </fieldset>
      </r-cell>
    </r-grid>

    <r-grid>
      <r-cell span="row" v-if="!store.list.length">
        <h2>Nothing in list</h2>
      </r-cell>
    </r-grid>
  </ClientOnly>
</template>

<style>
.checked span {
  text-decoration: line-through;
}

.checkbox-container {
  display: flex;
}

.name {
  margin-left: 1em;
  display: block;
}
</style>
