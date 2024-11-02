以下に、a・bの説明に基づいた、Vuexを使った一般的なコード例を示します。

この例では、**ユーザーの名前と年齢を管理するVuexストア**を作成し、`state`、`mutations`、`actions`、`getters` を使ってデータの取得と更新を行います。また、コンポーネント側で`computed`を使用して、Vuexの`state`と`getters`にアクセスします。

---

### Vuexストアの定義 (store.js)

まず、`store.js`でVuexストアを設定し、`state`、`mutations`、`actions`、`getters`を定義します。

```javascript
// store.js
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);

export default new Vuex.Store({
  state: {
    // ユーザー情報を保持するstate
    user: {
      name: 'Alice',
      age: 25
    }
  },
  mutations: {
    // ユーザー名を更新するミューテーション
    setUserName(state, name) {
      state.user.name = name;
    },
    // ユーザー年齢を更新するミューテーション
    setUserAge(state, age) {
      state.user.age = age;
    }
  },
  actions: {
    // 非同期でユーザー名を更新するアクション
    updateUserName({ commit }, name) {
      setTimeout(() => {
        commit('setUserName', name); // 非同期処理後にcommit
      }, 1000);
    },
    // 非同期でユーザー年齢を更新するアクション
    updateUserAge({ commit }, age) {
      setTimeout(() => {
        commit('setUserAge', age); // 非同期処理後にcommit
      }, 1000);
    }
  },
  getters: {
    // ユーザー名を取得するゲッター
    userName: (state) => state.user.name,
    // ユーザー年齢を取得するゲッター
    userAge: (state) => state.user.age,
    // 年齢に基づいて成人かどうかを判断するゲッター
    isAdult: (state) => state.user.age >= 18
  }
});
```

### コンポーネントでVuexを利用する (UserProfile.vue)

次に、`UserProfile.vue` というコンポーネントを作成し、Vuex の `state`、`actions`、`getters` にアクセスします。`computed` プロパティを使って Vuex の `state` と `getters` を参照し、ボタンを使って `actions` を呼び出し、`state` を更新します。

```vue
<template>
  <div>
    <h1>User Profile</h1>
    <p>名前: {{ userName }}</p>
    <p>年齢: {{ userAge }}</p>
    <p>成人: {{ isAdult ? 'はい' : 'いいえ' }}</p>

    <!-- 名前更新のための入力とボタン -->
    <input v-model="newName" placeholder="新しい名前を入力" />
    <button @click="changeUserName">名前を更新</button>

    <!-- 年齢更新のための入力とボタン -->
    <input v-model.number="newAge" type="number" placeholder="新しい年齢を入力" />
    <button @click="changeUserAge">年齢を更新</button>
  </div>
</template>

<script lang="ts">
// Vuexのマッピング関数とコンポーネントデコレーターをインポート
import { Component, Vue } from 'vue-property-decorator';
import { mapGetters, mapActions } from 'vuex';

@Component({
  computed: {
    ...mapGetters(['userName', 'userAge', 'isAdult']) // gettersをcomputedにマッピング
  },
  methods: {
    ...mapActions(['updateUserName', 'updateUserAge']) // actionsをmethodsにマッピング
  }
})
export default class UserProfile extends Vue {
  // 名前と年齢の入力値を保持するローカルデータ
  newName = '';
  newAge = 0;

  // 名前を更新するアクションを実行
  changeUserName() {
    this.updateUserName(this.newName);
  }

  // 年齢を更新するアクションを実行
  changeUserAge() {
    this.updateUserAge(this.newAge);
  }
}
</script>
```

---

### コードの説明

- **`state`**:
  - グローバルな状態として `user` オブジェクトを定義し、ユーザーの `name` と `age` を保持します。

- **`mutations`**:
  - `setUserName` と `setUserAge` の `mutations` を用意し、`state` の `user` オブジェクトの `name` や `age` を更新します。
  - これにより、`state` を直接操作せずに変更が可能になります。

- **`actions`**:
  - `updateUserName` と `updateUserAge` の `actions` を定義し、非同期に `commit` で `mutations` を呼び出して`state`を更新しています。
  - 例えば、API からのデータ取得後に更新するなど、非同期処理が必要な場面で使われます。

- **`getters`**:
  - `userName` と `userAge` の他に、年齢が 18 歳以上かどうかを判断する `isAdult` を定義し、条件付きデータ取得のような処理を行います。
  - `getters` によって、複数のコンポーネントでデータの取得や加工が一貫して行えます。

- **`computed`**:
  - `mapGetters` を使って `userName`、`userAge`、`isAdult` を `computed` プロパティとして使用することで、リアクティブにデータを取得できます。

- **`methods`**:
  - `mapActions` を使い、`updateUserName` や `updateUserAge` を `methods` として呼び出し、ボタンのクリックで `actions` をトリガーして `mutations` を実行しています。

---

### まとめ

- このコード例では、`state` をグローバルに管理し、`mutations` や `actions` で管理された方法でのみ変更を加えています。
- また、`getters` を通じて、必要なデータを柔軟に加工して取得し、`computed` を使ってコンポーネント内でリアクティブに利用しています。