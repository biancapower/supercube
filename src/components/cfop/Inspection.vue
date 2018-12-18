<template>
    <v-card color="blue-grey lighten-4" ripple class="card-inspection">
        <v-card-title class="display-2">
            Inspection<v-spacer />{{(value() / 1000).toFixed(2)}}
        </v-card-title>
    </v-card>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import { EventHub, Events } from '@/classes/event-hub'
import Timer from '@/classes/timer'
import CubeState from '@/classes/cube-state'

@Component
export default class Inspection extends Vue {
    private time = 0
    private active = false
    private duration = 5000

    private mounted() {
        EventHub.$on(Events.cubeScrambled, () => this.onCubeScrambled())
        EventHub.$on(Events.solveStarted, () => this.onSolveStarted())
        EventHub.$on(Events.solveCancelled, () => this.stop())
    }

    private value (): number {
      return Math.max(this.duration - this.time, 0)
    }

    private onCubeScrambled() {
        this.time = 0
        this.active = true
        window.requestAnimationFrame(this.timer)
    }

    private onSolveStarted() {
        this.stop()
        this.time = Timer.getInspectionTime()
    }

    private timer() {
        if (this.value() <= 0) {
          this.stop()
        }

        this.time = Timer.getInspectionTime()
        if (this.active) {
            window.requestAnimationFrame(this.timer)
        }
    }

    private stop() {
        this.active = false
    }
}
</script>
