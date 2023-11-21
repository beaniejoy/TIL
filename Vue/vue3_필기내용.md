# Vue 3 필기 내용

- vue3 환경에서 composition API를 제공해주는데 이것을 기본으로 사용하는 것으로

<br>

## refs


### v-for와 ref 같이 사용하기
```js
<v-sheet
  v-for="(category) in cafeMenuCategories" 
  :key="`category_${category.menuCategoryId}`"
  ref="categoryRefs"
  border
  class="py-4"
>
...content
</v-sheet>

<script setup>
const categoryRefs = ref([])

function moveToCategoryDirectly(index) {
  categoryRefs.value[index]
    .$el
    .scrollIntoView({ behavior: 'smooth' })
}
</script>
```
ref를 v-for와 연동해서 사용할 때 `ref([])`해서 사용  
`scrollIntoView`는 `$el`에서 사용 가능(원하는 ref의 element로 scroll 이동)  

<br>

## defineExpose

> Components using `<script setup>` are closed by default
> vue3에서 script setup으로 사용하는 컴포넌트에서는 그 안에 선언한 내용들에 접근 불가  

상위 컴포넌트에서 하위 컴포넌트에 있는 variable, methods 등에 직접 접근하고 싶을 때 사용 가능  

```js
// 하위 컴포넌트
const categoryRefs = ref([])

const moveToCategoryDirectly = (index) => {
  categoryRefs.value[index]
    .$el
    .scrollIntoView({ behavior: 'smooth' })
}

defineExpose({
  categoryRefs,
  moveToCategoryDirectly
})

// 상위 컴포넌트
<CafeDetailCategory
  ref="cafeDetailCategory"
  :cafe-menu-categories="cafeDetail.cafeMenuCategories" 
/>

//...
this.$refs.cafeDetailCategory.moveToCategoryDirectly(categoryIndex)
```
하위 컴포넌트에 ref를 걸어서 직접 접근 가능
