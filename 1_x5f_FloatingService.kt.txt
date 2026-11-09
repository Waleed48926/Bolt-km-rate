package com.waleed.plnperkm
import android.app.Service
import android.content.Intent
import android.graphics.PixelFormat
import android.view.Gravity
import android.view.MotionEvent
import android.view.View
import android.view.WindowManager
import android.widget.TextView
class FloatingService : Service() {
  companion object { var instance: FloatingService? = null
    fun update(p: Double, r: Double, pr: Double) { instance?.show(p, r, pr) } }
  private var wm: WindowManager? = null
  private var view: View? = null
  private var tvKm: TextView? = null
  private var tvRate: TextView? = null
  private var lp: WindowManager.LayoutParams? = null
  private var startX = 0; private var startY = 0; private var touchX = 0f; private var touchY = 0f
  override fun onCreate() { super.onCreate(); instance = this }
  override fun onBind(i: Intent?) = null
  fun show(pickup: Double, ride: Double, price: Double) {
    val total = pickup + ride; if (total <= 0) return; val rate = price / total
    if (view == null) {
      wm = getSystemService(WINDOW_SERVICE) as WindowManager
      lp = WindowManager.LayoutParams(-2, -2, 2038, 40, PixelFormat.TRANSLUCENT)
      lp!!.gravity = Gravity.TOP or Gravity.START; lp!!.x = 24; lp!!.y = 180
      view = layoutInflater.inflate(R.layout.widget_floating, null)
      tvKm = view!!.findViewById(R.id.tvKm); tvRate = view!!.findViewById(R.id.tvRate)
      view!!.setOnTouchListener { _, ev ->
        when (ev.action) {
          MotionEvent.ACTION_DOWN -> { startX = lp!!.x; startY = lp!!.y; touchX = ev.rawX; touchY = ev.rawY; true }
          MotionEvent.ACTION_MOVE -> { lp!!.x = startX + (ev.rawX - touchX).toInt(); lp!!.y = startY + (ev.rawY - touchY).toInt(); wm!!.updateViewLayout(view, lp); true }
          else -> false } }
      wm!!.addView(view, lp) }
    tvKm?.text = total.toString() + " km"
    tvRate?.text = rate.toString() + " PLN/km"
    view?.visibility = View.VISIBLE }
}
