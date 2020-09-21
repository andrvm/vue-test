<template>

    <div class="selector">

        <!-- First row -->
        <div class="row">

            <div class="col-md-2 d-flex align-items-center">
                <span>Категории</span>
            </div>

            <template v-if="!isSelectedFirstCategory">
                <div class="col-md-4">
                    <select class="form-control" v-model="selectedCategory1">
                        <option value="0">- Выберите категорию -</option>
                        <option v-for="(item, index) in categoryList1" :key="index" :value="item">
                            {{item.title}}
                        </option>
                    </select>
                </div>
                <div class="col-md-4" v-if="selectedCategory1">
                    <select class="form-control" v-model="selectedSubCategory1"
                            @change="isSelectedFirstCategory = true">
                        <option value="0">- Выберите подкатегорию -</option>
                        <option v-for="(item, index) in selectedCategory1.childs" :key="index" :value="item">
                            {{item.title}}
                        </option>
                    </select>
                </div>
            </template>

            <template v-else>
                <div class="input-group col-md-8">
                    <input type="text" class="form-control"
                           disabled="true"
                           :value="selectedTitle">
                    <div class="input-group-append">
                        <span class="input-group-text text-danger" @click="clear">x</span>
                    </div>
                </div>
            </template>

        </div>

        <!-- Need more button -->
        <div class="row mt-2" v-if="needMore && !addAnotherCategory">
            <div class="col-md-2"><!-- --></div>
            <div class="col-md-8">
                <button type="button" @click="addAnotherCategory = true">+ Добавить еще одну категорию</button>
            </div>
        </div>

        <!-- Second row -->
        <div class="row mt-2" v-if="addAnotherCategory">

            <div class="col-md-2 d-flex align-items-center">
               <!-- -->
            </div>

            <div class="col-md-4">
                <select class="form-control" v-model="selectedCategory2">
                    <option value="0">- Выберите категорию -</option>
                    <option v-for="(item, index) in categoryList2" :key="index" :value="item">
                        {{item.title}}
                     </option>
                </select>
            </div>

            <div class="col-md-4" v-if="selectedCategory1">
                <select class="form-control" v-model="selectedSubCategory2">
                    <option value="0">- Выберите подкатегорию -</option>
                    <option v-for="(item, index) in selectedCategory2.childs" :key="index" :value="item">
                        {{item.title}}
                    </option>
                </select>
            </div>

            <div class="col-md-2 d-flex align-items-center">
                <span class="text-danger cursor-pointer" @click="addAnotherCategory = false">x</span>
            </div>

        </div>

    </div>

</template>

<script>

import FakeDataService from '../core/services/fake-data.service';
import { tap } from 'rxjs/operators';

export default {
  name: 'Selector',
  data() {
    return {
        categoryList1: [],
        selectedCategory1: 0,
        selectedSubCategory1: 0,
        selectedCategory2: 0,
        selectedSubCategory2: 0,
        addAnotherCategory: false,
        isSelectedFirstCategory: false
    }
  },
  methods: {
    clear() {
        this.isSelectedFirstCategory = false;
        this.addAnotherCategory = false;
    }
  },
  mounted() {
    this.$subscribeTo(
            FakeDataService
                .getData()
                    .pipe(
                        tap( d => this.categoryList1 = d),
                    )
            );
  },
  computed: {
    categoryList2() {
        if ( !this.selectedCategory1 ) {
            return;
        }
        return this.categoryList1.filter(c => c.id !== this.selectedCategory1.id);
    },
    selectedTitle() {
        return this.isSelectedFirstCategory
            ? this.selectedCategory1.title + ' / ' + this.selectedSubCategory1.title
            : '';
    },
    needMore() {
        return this.isSelectedFirstCategory && !this.selectedSubCategory1.params
            ? true
            : false;
    }
  }
}
</script>


<style lang="scss">

    .selector {
        border: 1px solid #dedede;
        padding: 10px;
        background: #f8fafc;
        .form-control {
            height: calc(2.05rem) !important;
        }
        input {
            &.form-control {
                color: #31a6dd;
            }
        }
        .input-group-text {
            border-radius: 0;
            padding: 0 10px;
            border-left: 0;
            z-index: 999;
            cursor: pointer;
        }
        button {
            background: #e9ecef;
            border: 1px dashed #a2a3ab;
            color: #a2a3ab;
            cursor: pointer;
        }
    }

    .cursor-pointer{
        cursor: pointer;
    }

    @media(max-width: 480px) {
        .selector {
            background: red;
        }
    }
</style>
