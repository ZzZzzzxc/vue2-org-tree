<script>
const EVENTS = {
  click: "labelClick",
};
export default {
  name: "Node",
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
    toggleShow() {
      this.show = !this.show;
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
          class="label"
          draggable={true}
          onClick={() => this.$emit(EVENTS.click, data)}
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
          style={this.show ? "display:table" : "display:none"}
        >
          {data[children].map(child => (
            <Node
              customLabel={customLabel}
              class={child[children] ? "" : "leaf"}
              data={child}
              format={format}
              onLabelClick={labelClick}
            />
          ))}
        </div>
      ) : null;
    },
  },
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
