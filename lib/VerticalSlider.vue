<template>
    <div class="v-slider-input-vertical-slider" @pointerdown="startDrag" :style="{boxSizing: 'border-box'}">

        <div ref="trackElement" class="v-slider-input-vertical-track" :style="{
            ...{
                backgroundImage: `linear-gradient(to top, ${props.progressColor} ${progress()}%, transparent 0%)`,
                width: `${props.width}px`, height: `${props.height}px`, backgroundColor: 'lightgrey', borderRadius: '5px'
            },
            ...props.trackStyle
        }">

            <div class="v-slider-input-vertical-thumb" :style="{
                ...{
                    left: `${thumbLeft()}px`, top: `${thumbTop()}px`, position: 'relative', cursor: 'pointer',
                    width: `${props.thumbWidth}px`, height: `${props.thumbHeight}px`, background: 'red', borderRadius: '50%'
                },
                ...props.thumbStyle
            }">
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref } from 'vue';

const props = defineProps({
    progressColor: {
        type: String,
        default: 'red'
    },
    width: {
        type: Number,
        default: 10
    },
    height: {
        type: Number,
        default: 300
    },
    thumbWidth: {
        type: Number,
        default: 20
    },
    thumbHeight: {
        type: Number,
        default: 20
    },
    min: {
        type: Number,
        default: 0
    },
    max: {
        type: Number,
        default: 100
    },
    step: {
        type: Number,
        default: 1,
    },
    trackStyle: {
        type: Object,
        default: {}
    },
    thumbStyle: {
        type: Object,
        default: {}
    }
});

const model = defineModel({ default: 0 });
const trackElement = ref(null);
const dragging = ref(false);

document.addEventListener('pointermove', e => {
    if (!dragging.value) return;
    drag(e);
});

document.addEventListener('pointerup', stopDrag);

const progress = () => {
    const realTrackHeight = props.height - props.thumbHeight;
    return (((realTrackHeight - thumbTop()) + (props.thumbHeight / 2)) / props.height) * 100;
}

const thumbTop = () => {
    const realTrackHeight = props.height - props.thumbHeight;

    if (model.value <= props.min) {
        return realTrackHeight;
    } else if (model.value >= props.max) {
        return 0;
    } else {
        const percentage = 1 - ((model.value - props.min) / (props.max - props.min));
        return percentage * realTrackHeight;
    }
}

const thumbLeft = () => {
    if (props.width > props.thumbWidth) {
        return (props.width / 2) - (props.thumbWidth / 2);
    } else if (props.width < props.thumbWidth) {
        return -(props.thumbWidth / 2) + (props.width / 2);
    } else {
        return 0;
    }
}

function startDrag(e) {
    dragging.value = true;
    drag(e);
}

function stopDrag() {
    dragging.value = false;
}

function drag(e) {
    const box = trackElement.value.getBoundingClientRect();
    const halfThumb = props.thumbHeight / 2;
    const realTrackEnd = box.top + halfThumb;
    const realTrackStart = (box.top + box.height) - halfThumb;
    const realTrackHeight = box.height - props.thumbHeight;
    const offsetY = e.y - box.top;

    if (e.y >= realTrackStart) {
        model.value = props.min;
    } else if (e.y <= realTrackEnd) {
        model.value = props.max;
    } else {
        const percentage = 1 - ((offsetY - halfThumb) / realTrackHeight);
        const val = props.min + (props.max - props.min) * percentage;
        model.value = props.step * Math.round(val / props.step);
    }
}

</script>