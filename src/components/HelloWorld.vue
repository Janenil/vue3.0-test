<template>
  <div>
    <div ref="refDiv">templete:refDiv => data.a:{{a}}</div>
    <p ref="test-ref">templete:test-ref => test: {{test}}</p>
    <p>data.b.c => {{b.c}} <br> data_b_c => {{data_b_c}}</p>
    <p>testnormalObj.aa => {{aa}} <br> data.d => {{d}}</p>
    <p>computed:plusOne => {{plusOne}}</p>
    <button @click="handleclick(a)">更改testnormalObj.aa<br>  data.b.c <br> plusOne</button>
    <br>
    <button @click="handletestWatch">测试watch属性 data.a</button>
    <br>
    <button @click="handletestWatch1">测试watch属性test</button>
  </div>
</template>
<script>
import { 
  ref, 
  onMounted, 
  reactive, 
  toRefs, 
  computed,
  onBeforeMount,
  onBeforeUpdate,
  onUpdated,
  onBeforeUnmount,
  onUnmounted,
  onErrorCaptured,
  watch
 } from '@vue/composition-api'
export default {
  onRenderTriggered(e) {
    console.log('onRenderTriggered',e)
  },
  onRenderTracked(e) {
    console.log('onRenderTracked',e)
  },
  setup(props, context) {
    // 创建一个 DOM 引用
    const refDiv = ref(null)
    const testnormalObj = toRefs({aa:0,bb:'b'})
    const test = ref('test ref')
    const data = reactive({
      a:'test reactive',
      b:{
        c: 123
      },
      d: '123'
    })
    const data_b_c = computed(() => {
      return data.b.c + 101;
    })
    // 创建一个 computed 计算属性
    const plusOne = computed({
      // 取值函数
      get: () => data.b.c + 250,
      // 赋值函数
      set: val => {
        data.b.c += val
      }
    })

    const handleclick = (aa) => {
       console.log('handleclick', aa,data_b_c.value)
       testnormalObj.aa++
       data.b.c++
       plusOne.value = 250
    }
    const handletestWatch = () => {
      data.a = 'test watch' + Math.random()
    }
    const handletestWatch1 = () => {
      test.value = 'test watch ref' + Math.random()
    }

    watch(test, (val, oldV)=> {
      console.log('watch ref',val, oldV)
    }, {
      lazy: true
    })

    watch(() => 
      data.a, (val, oldV)=> {
        console.log('watch reactive',val, oldV)
      }
    )
    onBeforeMount(()=> {
      console.log('onBeforeMount!')
    })
    // 在 DOM 首次加载完毕之后，才能获取到元素的引用
    onMounted(() => {
      console.log('onMount!', context)
      // refDiv.value 是原生DOM对象
      refDiv.value.style.fontSize = 40 + 'px'
      context.refs['test-ref'].style.color = 'red'
      console.log('ref',test)
      console.log('reactive',data)
      console.log('torefs reactive',toRefs(data))
      console.log('torefs testnormalObj',toRefs(testnormalObj))
      console.log('computed',data_b_c)
    })
    
    onBeforeUpdate(()=> {
      console.log('onBeforeUpdate!')
    })
    onUpdated(() => {
      console.log('updated!')
    })
    
    onUnmounted(() => {
      console.log('unmounted!')
    })
    onBeforeUnmount(()=> {
      console.log('onBeforeUnmount!')

    })
    onErrorCaptured(()=> {
      console.log('onErrorCaptured!')

    })

    // 把创建的引用 return 出去
    return {
      refDiv,
      ...toRefs(testnormalObj),
      ...toRefs(data),
      test,
      props,
      handleclick,
      handletestWatch,
      handletestWatch1,
      data_b_c,
      plusOne
    }
  }
}
</script>

<style>
button {
  background-color: #fff;
  border: 2px solid #999;
  color: #666;
  border-radius: 5px;
  margin-bottom: 10px;
  padding: 7px 20px;
}
button:hover {
  background-color: #999;
  color: #fff;
}
</style>
