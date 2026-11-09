package com.waleed.plnperkm
import android.accessibilityservice.AccessibilityService
import android.view.accessibility.AccessibilityEvent
import android.view.accessibility.AccessibilityNodeInfo
class OfferReaderService : AccessibilityService() {
  override fun onAccessibilityEvent(e: AccessibilityEvent?) {
    val pkg = e?.packageName?.toString() ?: return
    if (!pkg.contains("bolt") && !pkg.contains("uber")) return
    val root = rootInActiveWindow ?: return
    val texts = mutableListOf<String>(); collect(root, texts)
    val joined = texts.joinToString(" ")
    val kms = Regex("[0-9]+[.,]?[0-9]*").findAll(joined).mapNotNull { it.value.replace(",",".").toDoubleOrNull() }.toList()
    val price = Regex("[0-9]+[.,][0-9]{2}").find(joined)?.value?.replace(",",".")?.toDoubleOrNull() ?: return
    if (kms.size >= 2) FloatingService.update(kms[0], kms[1], price) }
  override fun onInterrupt() {}
  fun collect(n: AccessibilityNodeInfo, out: MutableList<String>) { n.text?.toString()?.let { out.add(it) }; for (i in 0 until n.childCount) { n.getChild(i)?.let { collect(it, out) } } }
}
