<template>
  <ul :class="simpleWrapClasses" :style="styles" v-if="simple">
    <li :title="Previous" :class="prevClasses" @click="prev">
      <a>
        <bc-icon type="chevron-left"></bc-icon></a>
    </li>
    <div :class="simplePagerClasses" :title="currentPage + '/' + allPages">
      <input type="text" :value="currentPage" @keydown="keyDown" @keyup="keyUp" @change="keyUp">
      <span>/</span> {{ allPages }}
    </div>
    <li :title="Next" :class="nextClasses" @click="next">
      <a><bc-icon type="chevron-right"></bc-icon></a>
    </li>
  </ul>
   <ul :class="wrapClasses" :style="styles" v-else>
    <span :class="[prefixCls + '-total']" v-if="showTotal">
      <slot>Total {{ total }} <template v-if="total <= 1">items</template>
      <template v-else>items</template></slot>
    </span>
    <li
      title="Previous"
      :class="prevClasses"
      @click="prev">
      <a>
       <bc-icon type="chevron-left"></bc-icon>
      </a>
    </li>
    <li
      title="1"
      :class="firstPageClasses"
      @click="changePage(1)">
      <a>1</a>
    </li>
    <li
      title="向前 5 页"
      v-if="currentPage - 3 > 1"
      :class="[prefixCls + '-item-jump-prev']"
      @click="fastPrev">
      <a><bc-icon type="chevron-left-double"></bc-icon></a>
      </li>
    <li
      :title="currentPage - 2"
      v-if="currentPage - 2 > 1"
      :class="[prefixCls + '-item']"
      @click="changePage(currentPage - 2)">
      <a>{{ currentPage - 2 }}</a>
    </li>
    <li
      :title="currentPage - 1"
      v-if="currentPage - 1 > 1"
      :class="[prefixCls + '-item']"
      @click="changePage(currentPage - 1)"><a>{{ currentPage - 1 }}</a></li>
    <li
      :title="currentPage"
      v-if="currentPage != 1 && currentPage != allPages"
      :class="[prefixCls + '-item',prefixCls + '-item-active']"><a>{{ currentPage }}</a></li>
    <li
      :title="currentPage + 1"
      v-if="currentPage + 1 < allPages"
      :class="[prefixCls + '-item']"
      @click="changePage(currentPage + 1)"><a>{{ currentPage + 1 }}</a></li>
    <li
      :title="currentPage + 2"
      v-if="currentPage + 2 < allPages"
      :class="[prefixCls + '-item']"
      @click="changePage(currentPage + 2)"><a>{{ currentPage + 2 }}</a></li>
    <li
      title="向后 5 页"
      v-if="currentPage + 3 < allPages"
      :class="[prefixCls + '-item-jump-next']"
      @click="fastNext"><a><bc-icon type="chevron-right-double"></bc-icon></a></li>
    <li
      :title="allPages" v-if="allPages > 1"
      :class="lastPageClasses" @click="changePage(allPages)"><a>{{ allPages }}</a></li>
    <li
      title="Next"
      :class="nextClasses" @click="next">
      <a><bc-icon type="chevron-right"></bc-icon></a>
    </li>
    <li class="bcui-page-item-options">
      <bc-page-item
        :show-sizer="showSizer"
        :page-size="currentPageSize"
        :page-size-opts="pageSizeOpts"
        :show-elevator="showElevator"
        :_current.once="currentPage"
        :current="currentPage"
        :all-pages="allPages"
        :is-small="isSmall"
        @on-size="onSize"
        @on-page="onPage">
      </bc-page-item>
    </li>
  </ul>
</template>
<script>
  import {
    oneOf,
  } from '../../utils/tools';
  import Options from './options.vue';
  import Icon from '../icon';

  const prefixCls = 'bcui-page';

  export default {
    name: 'bc-pagination',
    components: {
      'bc-page-item': Options,
      'bc-icon': Icon,
    },
    props: {
      current: {
        type: Number,
        default: 1,
      },
      total: {
        type: Number,
        default: 0,
      },
      pageSize: {
        type: Number,
        default: 10,
      },
      pageSizeOpts: {
        type: Array,
        default() {
          return [10, 20, 30, 40];
        },
      },
      size: {
        validator(value) {
          return oneOf(value, ['small']);
        },
      },
      simple: {
        type: Boolean,
        default: false,
      },
      showTotal: {
        type: Boolean,
        default: false,
      },
      showElevator: {
        type: Boolean,
        default: false,
      },
      showSizer: {
        type: Boolean,
        default: false,
      },
      className: {
        type: String,
      },
      styles: {
        type: Object,
      },
    },
    data() {
      return {
        prefixCls: prefixCls,
        currentPage: this.current,
        currentPageSize: this.pageSize,
      };
    },
    watch: {
      current(val) {
        this.currentPage = val;
      },
      pageSize(val) {
        this.currentPageSize = val;
      },
    },
    computed: {
      isSmall() {
        return !!this.size;
      },
      allPages() {
        const allPage = Math.ceil(this.total / this.currentPageSize);
        return (allPage === 0) ? 1 : allPage;
      },
      simpleWrapClasses() {
        return [
          `${prefixCls}`,
          `${prefixCls}-simple`, {
            [`${this.className}`]: !!this.className,
          },
        ];
      },
      simplePagerClasses() {
        return `${prefixCls}-simple-pager`;
      },
      wrapClasses() {
        return [
          `${prefixCls}`, {
            [`${this.className}`]: !!this.className,
            'mini': !!this.size,
          },
        ];
      },
      prevClasses() {
        return [
          `${prefixCls}-prev`, {
            [`${prefixCls}-disabled`]: this.currentPage === 1,
          },
        ];
      },
      nextClasses() {
        return [
          `${prefixCls}-next`, {
            [`${prefixCls}-disabled`]: this.currentPage === this.allPages,
          },
        ];
      },
      firstPageClasses() {
        return [
          `${prefixCls}-item`, {
            [`${prefixCls}-item-active`]: this.currentPage === 1,
          },
        ];
      },
      lastPageClasses() {
        return [
          `${prefixCls}-item`, {
            [`${prefixCls}-item-active`]: this.currentPage === this.allPages,
          },
        ];
      },
    },
    methods: {
      changePage(page) {
        if (this.currentPage != page) {
          this.currentPage = page;
          this.$emit('on-change', page);
        }
      },
      prev() {
        const current = this.currentPage;
        if (current <= 1) {
          return false;
        }
        this.changePage(current - 1);
      },
      next() {
        const current = this.currentPage;
        if (current >= this.allPages) {
          return false;
        }
        this.changePage(current + 1);
      },
      fastPrev() {
        const page = this.currentPage - 5;
        if (page > 0) {
          this.changePage(page);
        } else {
          this.changePage(1);
        }
      },
      fastNext() {
        const page = this.currentPage + 5;
        if (page > this.allPages) {
          this.changePage(this.allPages);
        } else {
          this.changePage(page);
        }
      },
      onSize(pageSize) {
        this.currentPageSize = pageSize;
        this.changePage(1);
        this.$emit('on-page-size-change', pageSize);
      },
      onPage(page) {
        this.changePage(page);
      },
      keyDown(e) {
        const key = e.keyCode;
        const condition = (key >= 48 && key <= 57) || key == 8 || key == 37 || key == 39;

        if (!condition) {
          e.preventDefault();
        }
      },
      keyUp(e) {
        const key = e.keyCode;
        const val = parseInt(e.target.value);

        if (key === 38) {
          this.prev();
        } else if (key === 40) {
          this.next();
        } else if (key == 13) {
          let page = 1;

          if (val > this.allPages) {
            page = this.allPages;
          } else if (val <= 0) {
            page = 1;
          } else {
            page = val;
          }

          e.target.value = page;
          this.changePage(page);
        }
      },
    },
  };
</script>
