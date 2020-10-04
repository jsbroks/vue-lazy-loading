<template>
  <canvas ref="canvas" class="w-full h-full rounded" @wheel="onMouseWheel" />
</template>

<script>
import { ref, onMounted } from 'vue'

import paper from 'paper'

function calculateViewTransformation(delta, p, view, zoomMagnification) {
  const oldZoom = view.zoom
  const c = view.center
  const factor = 1 + zoomMagnification
  const zoom = delta < 0 ? oldZoom * factor : oldZoom / factor
  const beta = oldZoom / zoom
  const pc = p.subtract(c)
  const offset = p.subtract(pc.multiply(beta)).subtract(c)
  return { zoom, offset }
}

const createVueLogo = () => {
  const logo = new paper.Path(
    // svg path for the 'V'
    'm0 0-22.669-39.264-22.669 39.264h-75.491l98.16-170.02 98.16 170.02z'
  )
  logo.rotate(180)
  logo.fillColor = '#41b883'
  logo.position = paper.view.center
  return logo
}

export default {
  setup() {
    const canvas = ref(null)
    const logo = ref(null)

    // Initalize canvas and render svg
    onMounted(() => {
      paper.setup(canvas.value)

      const { view } = paper
      view.onMouseDown = onMouseDown

      logo.value = createVueLogo()
      logo.value.onMouseDrag = onMouseDrag
    })

    // Apply zoom transfromation
    const onMouseWheel = event => {
      event.preventDefault({ passive: true })
      const { view } = paper
      const { pageX, pageY, currentTarget, deltaY } = event
      const { left, top } = currentTarget.getBoundingClientRect()

      const viewPosition = view.viewToProject(
        new paper.Point(pageX - left, pageY - top)
      )

      const transform = calculateViewTransformation(
        deltaY,
        viewPosition,
        view,
        0.2
      )
      view.zoom = transform.zoom
      view.setCenter(view.center.add(transform.offset))
    }

    // Used to calculate drag delta
    const downPoint = ref(new paper.Point(0, 0))
    const onMouseDown = event => {
      downPoint.value = event.point
    }

    // Handle click and drag event
    const onMouseDrag = event => {
      const { view } = paper
      const oldCenter = paper.view.center
      view.center = view.center.add(downPoint.value.subtract(event.point))
      downPoint.value = event.point.add(view.center.subtract(oldCenter))
    }

    return { canvas, onMouseWheel }
  }
}
</script>
