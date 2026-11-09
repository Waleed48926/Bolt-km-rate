package com.waleed.plnperkm
import android.content.Intent
import android.os.Bundle
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
class MainActivity : AppCompatActivity() {
  override fun onCreate(s: Bundle?) { super.onCreate(s)
    val tv = TextView(this); tv.text = "Allow Display over other apps + Accessibility > PLN per KM. Then open Bolt/Uber."; tv.textSize = 18f; tv.setPadding(48,80,48,48); setContentView(tv)
    startService(Intent(this, FloatingService::class.java)) } }
