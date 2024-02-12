<template>
    <div class="v-slider-input-slider" @pointerdown="startDrag" :style="{boxSizing: 'border-box'}">

        <div ref="trackElement" class="v-slider-input-track" :style="{
            ...{
                backgroundImage: `linear-gradient(to right, ${props.progressColor} ${progress()}%, transparent 0%)`,
                width: `${props.width}px`, height: `${props.height}px`, backgroundColor: 'lightgrey', borderRadius: '5px'
            },
            ...props.trackStyle
        }">

            <div class="v-slider-input-thumb" :style="{
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
        default: 300
    },
    height: {
        type: Number,
        default: 10
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
    return ((thumbLeft() + (props.thumbWidth / 2)) / props.width) * 100;
}

const thumbLeft = () => {
    const realTrackWidth = props.width - props.thumbWidth;

    if (model.value <= props.min) {
        return 0;
    } else if (model.value >= props.max) {
        return realTrackWidth;
    } else {
        const percentage = ((model.value - props.min) / (props.max - props.min));
        return percentage * realTrackWidth;
    }
}

const thumbTop = () => {
    if (props.height > props.thumbHeight) {
        return (props.height / 2) - (props.thumbHeight / 2);
    } else if (props.height < props.thumbHeight) {
        return -(props.thumbHeight / 2) + (props.height / 2);
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
    const halfThumb = props.thumbWidth / 2;
    const realTrackStart = box.left + halfThumb;
    const realTrackEnd = (box.left + box.width) - halfThumb;
    const realTrackWidth = box.width - props.thumbWidth;
    const offsetX = e.x - box.left;

    if (e.x <= realTrackStart) {
        model.value = props.min;
    } else if (e.x >= realTrackEnd) {
        model.value = props.max;
    } else {
        const percentage = (offsetX - halfThumb) / realTrackWidth;
        const val = props.min + (props.max - props.min) * percentage;
        model.value = props.step * Math.round(val / props.step);
    }
}

</script>