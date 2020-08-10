<script>
const EVENTS = {
  click: "labelClick",
  change: "dataChange",
};
let draggable = null;
export default {
  name: "Node",
  model: {
    prop: "data",
    event: EVENTS.change,
  },
  props: {
    data: {
      type: Object,
      required: true,
    },
    format: Object,
    customLabel: Function,
  },
  data() {
    return {
      show: true,
    };
  },
  methods: {
    onNodeClick(...args) {
      this.$emit(EVENTS.click, ...args);
    },
    onDataChange(data) {
      this.$emit(EVENTS.change, data);
    },
    toggleShow() {
      this.show = !this.show;
    },
    handleDragStart() {
      draggable = this;
    },
    handleDrag() {},
    handleDragend() {},
    handleEnter() {},
    handleOver(e) {
      // draggable 被拖拽元素 this 目的元素
      // 目的地为拖拽元素的子元素
      if (draggable.$el.contains(this.$el)) return;
      e.preventDefault();
    },
    handleLeave() {},
    handleDrop(e) {
      // draggable 被拖拽元素 this 目的元素
      const { label, children } = this.format;
      // 从出发点移除被移动元素
      const index = draggable.$parent.data[children].findIndex(child => {
        return child[label] === draggable.data[label];
      });
      draggable.$parent.data[children].splice(index, 1);
      // 目的地新增被移动元素
      if (this.data[children]) {
        this.data[children].push(draggable.data);
      } else {
        this.$set(this.data, children, [draggable.data]);
      }
      e.preventDefault();
    },
    renderHandleBtn() {
      return (
        <span class="btn" onClick={this.toggleShow}>
          {this.show ? "-" : "+"}
        </span>
      );
    },
    renderLabel() {
      const { customLabel, format, data } = this;
      const { label } = format;
      return (
        <div
          ref="drag"
          class="label"
          draggable={true}
          onClick={() => this.onNodeClick(data)}
          onDragstart={this.handleDragStart}
          onDrag={this.handleDrag}
          onDragend={this.handleDragend}
          onDragenter={this.handleEnter}
          onDragover={this.handleOver}
          onDragleave={this.handleLeave}
          onDrop={this.handleDrop}
        >
          {customLabel ? customLabel(data) : data[label]}
          {this.renderHandleBtn()}
        </div>
      );
    },
    renderChildren() {
      const { customLabel, format, data } = this;
      const { children } = format;
      const labelClick = this.$listeners[EVENTS.click];
      return data[children] ? (
        <div
          class="children"
          style={
            this.show && data[children] && data[children].length
              ? "display:table"
              : "display:none"
          }
        >
          {data[children].map(child => (
            <Node
              customLabel={customLabel}
              class={child[children] && child[children].length ? "" : "leaf"}
              data={child}
              format={format}
              onLabelClick={labelClick}
              onDataChange={this.onDataChange}
            />
          ))}
        </div>
      ) : null;
    },
  },
  mounted() {},
  render() {
    return (
      <div class="node">
        {this.renderLabel()}
        {this.renderChildren()}
      </div>
    );
  },
};
</script>

<style lang="scss" scoped>
.btn {
  cursor: pointer;
}
.leaf {
  padding-left: 10px;
  padding-right: 10px;
  .btn {
    display: none;
  }
}
.node {
  display: table-cell;
  vertical-align: top;
  padding-top: 20px;
  position: relative;
  .label {
    display: inline-block;
    white-space: nowrap;
  }
  .children {
    display: table;
    position: relative;
    padding-top: 20px;
    .node {
      &:before,
      &:after {
        content: "";
        position: absolute;
        top: 0;
        left: 0;
        width: 50%;
        height: 19px;
      }

      &:after {
        left: 50%;
        border-left: 1px solid #ddd;
      }

      &:not(:first-child):before,
      &:not(:last-child):after {
        border-top: 1px solid #ddd;
      }
    }
    &:before {
      content: "";
      position: absolute;
      top: 0;
      left: 50%;
      width: 0;
      height: 20px;
      border-left: 1px solid #ddd;
    }
    &:after {
      content: "";
      display: table;
      clear: both;
    }
  }
}
</style>
