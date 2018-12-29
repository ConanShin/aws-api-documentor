<template lang="pug">
 #app
   .api(v-for="api in apiList" :class="[api.method, {'clicked': api.isClicked}]")
     .basic-info(@click="toggleDetail(api)")
       .method {{ api.method.toUpperCase() }}
       .url {{ api.url }}
       .button(@click.stop.propagation="invokeApi(api)") Test
     .detail-info
       .detail-info-category(v-if="api.queryParameters") Query Parameters
       .detail-info-query-parameter(v-for="parameterKey in Object.keys(api.queryParameters)") {{ parameterKey }}
         input(:ref="parameterKey" :placeholder="api.queryParameters[parameterKey]")
       .detail-info-category(v-if="api.bodyParameters") Body Parameters
       .detail-info-body-parameter(v-for="parameterKey in Object.keys(api.bodyParameters)") {{ parameterKey }}
         input(:ref="parameterKey" :placeholder="JSON.stringify(api.bodyParameters[parameterKey])")
       .detail-info-category Response
       .detail-info-test-response {{ api.response }}
</template>

<script>
import {Vue, Component} from 'vue-property-decorator'
import axios from 'axios'

@Component
export default class App extends Vue {
  apiList = []
  loginSuccess = false

  toggleDetail (api) {
    api.isClicked = !api.isClicked
  }

  objectToQueryParameter (object) {
    return Object.keys(object).map(key => key + '=' + object[key]).join('&')
  }

  invokeApi (api) {
    let url = api.url
    api.response = ''
    if (api.queryParameters) {
      const queryString = this.objectToQueryParameter(api.queryParameters)
      url += '?' + queryString
    }

    axios(url, {
      method: api.method,
      data: api.bodyParameters
    }).then(response => {
      api.response = response.data
      api.isClicked = true
    }).catch(error => {
      api.response = error
      api.isClicked = true
    })
  }

  beforeMount () {
    const json = require('json-loader!yaml-loader!./assets/serverless.yml')
    const list = Object
    .keys(json.functions)
    .filter(lambdaKey => json.functions[lambdaKey].events)
    .map(lambdaKey => {
      return json.functions[lambdaKey].events.map(event => {
        return {
          name: lambdaKey,
          url: event.http.path,
          method: event.http.method,
          queryParameters: event.http.queryParameters || '',
          bodyParameters: event.http.bodyParameters || '',
          isClicked: false,
          response: ''
        }
      })
    })
    this.apiList = [].concat.apply([], list);
  }
  mounted () {
    if (this.username) {
      this.loginSuccess = true
    }

    this.apiList.forEach(api => {
      if (api.queryParameters) {
        Object.keys(api.queryParameters).forEach(parameterKey => {
          this.$refs[parameterKey][0].value = JSON.stringify(api.queryParameters[parameterKey])
        })
      }
      if (api.bodyParameters) {
        Object.keys(api.bodyParameters).forEach(parameterKey => {
          this.$refs[parameterKey][0].value = JSON.stringify(api.bodyParameters[parameterKey])
        })
      }
    })
  }
}
</script>

<style lang="less">
 input {
   font-size: 14px;
   font-family: sans-serif;
 }

 .api {
   max-height: 52px;
   margin: 5px;
   padding: 0 15px;
   border-radius: 5px;
   font-family: sans-serif;
   transition: max-height 0.3s ease;
   overflow: hidden;

   &.clicked {
     max-height: 1000px;
   }
 }

 .method, .url, .button {
   display: inline-flex;
   align-items: center;
   height: 32px;
   margin: 10px 0;
 }

 .method {
   width: 80px;
   color: white;
   justify-content: center;
   border-radius: 5px;
   margin-right: 5px;
 }

 .button {
   float: right;
   height: 28px;
   border-radius: 4px;
   border: 2px solid gray;
   color: #3b4151;
   width: 60px;
   justify-content: center;
   cursor: pointer;
 }

 .detail-info {
   &-category {
     font-weight: bold;
     margin: 10px 0;
   }
   &-test-response {
     border: 2px solid gray;
     height: 126px;
     border-radius: 4px;
     margin-bottom: 10px;
     overflow: scroll;
   }
   input {
     display: block;
     height: 20px;
     width: 100%;
   }
 }

 .get {
   &.api {
     border: 1px solid #61affe;
   }
   .method {
     background: #61affe;;
   }
 }

 .post {
   &.api {
     border: 1px solid #49cc90;
   }
   .method {
     background: #49cc90;
   }
 }
</style>
