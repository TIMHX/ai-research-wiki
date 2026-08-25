---
title: CS6475 Computational Photography (course)
created: 2026-08-25
updated: 2026-08-25
type: concept
tags: [course, computer-vision]
sources: [https://omscs6475.cc.gatech.edu/course-syllabus/, https://omscs6475.cc.gatech.edu/course-schedule/]
---

# CS6475 Computational Photography (OMSCS, Fall 2026)

Georgia Tech OMSCS 计算摄影课程。学期 8/24-12/17。编程密集型，全部 deliverable 用 Python 实现，项目需 replicate 研究论文。

## 关键事实

- 截止 = 12am AoE = 美东当天 8:00 AM；quiz/exam/portfolio 无 late
- 评分: 5 作业 50% + 2 项目 30% + 期末考 10% + 3 notebook quiz 8% + plagiarism quiz 1% + portfolio 1%
- 作业 1 周/个，项目 2-3 周；晚交 48h 窗口内 10%/天，整份连坐
- ⚠️ 课程明文禁止 AI 写代码/报告（OSI 上报）；查重对比历届提交
- 官方 repo: github.gatech.edu/omscs6475/assignments；个人工作 repo: github.com/TIMHX/CS6475 (private)
- 环境: conda CS6475 (python 3.10.6, opencv 4.13, numpy 2.2.6, scipy 1.15.3, numba 0.65, matplotlib 3.10.9, pandas 2.3.3)

## Fall 2026 交付日程（美东）

| Due | Deliverable |
|---|---|
| 9/1 | Plagiarism Quiz + Notebook 1 Quiz |
| 9/8 | A1 Camera Obscura (无 coding，实验+LaTeX) |
| 9/15 | A2 Pyramid Blending (Burt & Adelson) |
| 9/22 | A3 Panoramas (SIFT 拼接) |
| 9/29 | Notebook 2 Quiz |
| 10/9 | Project 1 (replicate 论文，无 skeleton) |
| 10/20 | A4 HDR (Debevec & Malik) |
| 10/27 | A5 Video Textures (Schödl) |
| 11/3 | Notebook 3 Quiz |
| 11/20 | Project 2 |
| 12/1 | Final Portfolio |
| 12/8-11 | Final Exam window (开卷 cumulative) |

## 核心技术主题（与 wiki 概念关联）

- 图像金字塔/多分辨率融合 → Burt & Adelson (1983) Laplacian pyramid, spline
- 特征检测与匹配 → Harris corner, SIFT (Lowe 2004)
- 全景拼接 → Brown & Lowe (2003)
- HDR → Debevec & Malik (1997) radiance map recovery
- Video textures → Schödl et al. (2000)
- 计算相机/光场 → Levoy & Hanrahan (1996), Ng et al. (2005)
- Poisson image editing → Pérez et al. (2003)

## 链接

- Syllabus: https://omscs6475.cc.gatech.edu/course-syllabus/
- Schedule: https://omscs6475.cc.gatech.edu/course-schedule/
- Readings: https://omscs6475.cc.gatech.edu/reading-materials/ （Szeliski 教材 + 论文 PDF 列表）
- Avoiding plagiarism: https://omscs6475.cc.gatech.edu/avoiding-plagiarism/
- 详细操作指南见 skill: cs6475-computational-photography
