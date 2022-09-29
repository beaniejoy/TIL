# Vue Life Cycle

<br>

## 부모, 자식 component 간 생명주기

### created

- 부모 -> 자식 순서로 작동
- 중요한 것은 vue의 life cycle은 api 통신할 때 async - await 기다려주지 않는다.

```js
// parent component
<div>
  <child-comp :propData="propData"></child-comp>
</div>

new Vue({
  components: {
    'child-comp': childComponent
  },
  data() {
    return {
      propData: null
    }
  },
  async created() {
    // 1.
    const response = await axios.get(url)
      .catch(error => { 
        console.debug('error: ', error); 
        history.back();
      });

    this.propData = response.data;
  }
});


// child component
const childComponent = {
  template: ``,
  props: ['propData'],
  created() {
    console.log('propData', this.propData);
  }
}
```