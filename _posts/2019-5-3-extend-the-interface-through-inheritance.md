---
layout: post
title: 通过继承来拓展接口
categories: JavaSE
tags: 接口
author: fidemyuan
---

## 如何来拓展接口
通过继承，在接口中添加新的方法声明，还可以通过继承在新接口中组合数个接口。这两种情况都可以获得新的接口

	`
	interface Monster {// 怪物
		void menace();// 威胁
	}
	 
	interface DangerousMonster extends Monster {
		void destroy();// 破坏
	}
	 
	interface Lethal {// 致命的
		void kill();// 杀死
	}
	 
	class DragonZilla implements DangerousMonster {
	 
		@Override
		public void menace() {
			// TODO Auto-generated method stub
	 
		}
	 
		@Override
		public void destroy() {
			// TODO Auto-generated method stub
	 
		}
	}
	 
	interface Vampire extends DangerousMonster, Lethal {// 吸血鬼
		void drinkBlood();
	}
	 
	class VeryBadVampire implements Vampire {
		@Override
		public void menace() {
			// TODO Auto-generated method stub
	 
		}
	 
		@Override
		public void destroy() {
			// TODO Auto-generated method stub
	 
		}
	 
		@Override
		public void kill() {
			// TODO Auto-generated method stub
	 
		}
	 
		@Override
		public void drinkBlood() {
			// TODO Auto-generated method stub
	 
		}
	 
	}
	 
	public class HorrorShow {
		static void u(Monster b) {
			b.menace();
		}
	 
		static void v(DangerousMonster d) {
			d.menace();
			d.destroy();
		}
	 
		static void w(Lethal l) {
			l.kill();
		}
	 
		static void x(Vampire v) {
			v.menace();
			v.destroy();
			v.kill();
			v.drinkBlood();
		}
	 
		public static void main(String[] args) {
			DangerousMonster barney = new DragonZilla();
			u(barney);
			v(barney);
	 
			Vampire vlad = new VeryBadVampire();
			u(vlad);
			v(vlad);
			w(vlad);
			x(vlad);
	 
		}
	 
	}
