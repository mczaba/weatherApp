<template>
    <div class="toggle-wrapper">
        <label class="toggle">
            <input
                type="checkbox"
                :checked="(mode === dark) ? 'checked' : false"
                @change="$emit('toggle')" />
            <span class="toggler round"></span>
        </label>
    </div>
</template>

<script>
export default {
  props: ['mode'],
};
</script>

<style lang="scss" scoped>
.toggle {
    position: relative;
    display: inline-block;
    width: 50px;
    height: 28px;
}
.toggle input {
    opacity: 0;
    width: 0;
    height: 0;
}
.toggler {
    position: absolute;
    cursor: pointer;
    border-radius:28px;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: #36393f;
    transition: 0.4s;
}
.toggler::before{
    position: absolute;
    content: "";
    height: 22px;
    width: 22px;
    border-radius: 50%;
    left: 3px;
    bottom: 3px;
    background-color: #FFF;
    transition: 0.4s;
}

input:checked + .toggler{
    background-color: #2196F3;
}
input:focus + .toggler{
    box-shadow: 0 0 2px #2196F3;
}
input:checked + .toggler::before{
    transform: translateX(22px);
}

</style>
