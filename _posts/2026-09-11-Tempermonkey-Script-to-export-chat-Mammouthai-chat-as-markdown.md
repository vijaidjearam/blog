---
date: 2026-09-11 12:49:36
title: TemperMonkey Script to export the entire chat of mammouth.ai to a markdownfile 
category: TemperMonkey
tags: Tempermonkey javascript
---


Here is the script
```javascript
// ==UserScript==
// @name         Mammouth.ai Conversation Exporter
// @namespace    mammouth-exporter
// @version      2.0.0
// @description  Export Mammouth.ai conversations as Markdown, TXT, or JSON while preserving code blocks
// @match        https://mammouth.ai/*
// @match        https://www.mammouth.ai/*
// @grant        none
// @run-at       document-idle
// ==/UserScript==

(() => {
  "use strict";

  const BUTTON_ID = "mammouth-conversation-exporter";
  const MESSAGE_SELECTORS = [
    '[data-message-id]',
    '[data-testid*="message"]',
    '[class*="message"]',
    '[class*="Message"]',
    '[class*="conversation-turn"]',
    '[class*="ConversationTurn"]',
    '[class*="chat-message"]',
    '[class*="ChatMessage"]',
  ];

  const sleep = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

  function visible(element) {
    if (!element) return false;
    const style = getComputedStyle(element);
    const rect = element.getBoundingClientRect();

    return (
      style.display !== "none" &&
      style.visibility !== "hidden" &&
      rect.width > 0 &&
      rect.height > 0
    );
  }

  function cleanText(value) {
    return String(value || "")
      .replace(/\u200B/g, "")
      .replace(/\r\n/g, "\n")
      .replace(/\r/g, "\n")
      .replace(/[ \t]+\n/g, "\n")
      .replace(/\n{3,}/g, "\n\n")
      .trim();
  }

  function getLanguage(element) {
    const className = String(element?.className || "");
    const match = className.match(
      /(?:language|lang)-([a-zA-Z0-9_+#.-]+)/
    );

    return match ? match[1] : "";
  }

  function safeFence(code) {
    const runs = String(code)
      .split("\n")
      .flatMap((line) => line.match(/`+/g) || [])
      .map((run) => run.length);

    const length = Math.max(3, ...(runs.length ? runs : [0]) + 1);
    return "`".repeat(length);
  }

  function domToMarkdown(root) {
    const output = [];

    function walk(node) {
      if (node.nodeType === Node.TEXT_NODE) {
        output.push(node.nodeValue);
        return;
      }

      if (node.nodeType !== Node.ELEMENT_NODE) return;

      const tag = node.tagName.toLowerCase();

      if (
        ["script", "style", "noscript", "button", "textarea", "input", "select"]
          .includes(tag)
      ) {
        return;
      }

      if (tag === "pre") {
        const codeElement = node.querySelector("code");
        const code = codeElement
          ? codeElement.textContent
          : node.textContent;

        const language = getLanguage(codeElement || node);
        const fence = safeFence(code);

        output.push(
          `\n${fence}${language}\n${String(code).replace(/\s+$/, "")}\n${fence}\n`
        );
        return;
      }

      if (tag === "code") {
        output.push("`", node.textContent, "`");
        return;
      }

      if (tag === "br") {
        output.push("\n");
        return;
      }

      if (tag === "hr") {
        output.push("\n---\n");
        return;
      }

      if (/^h[1-6]$/.test(tag)) {
        const level = Number(tag.substring(1));
        output.push(`\n${"#".repeat(level)} `);
        node.childNodes.forEach(walk);
        output.push("\n");
        return;
      }

      if (tag === "strong" || tag === "b") {
        output.push("**");
        node.childNodes.forEach(walk);
        output.push("**");
        return;
      }

      if (tag === "em" || tag === "i") {
        output.push("*");
        node.childNodes.forEach(walk);
        output.push("*");
        return;
      }

      if (tag === "del" || tag === "s") {
        output.push("~~");
        node.childNodes.forEach(walk);
        output.push("~~");
        return;
      }

      if (tag === "blockquote") {
        const text = domToMarkdownChildren(node)
          .split("\n")
          .map((line) => `> ${line}`)
          .join("\n");

        output.push(`\n${text}\n`);
        return;
      }

      if (tag === "a") {
        const href = node.getAttribute("href");
        const text = cleanText(node.textContent);

        if (href && text) {
          output.push(`[${text}](${href})`);
        } else {
          node.childNodes.forEach(walk);
        }

        return;
      }

      if (tag === "img") {
        const src = node.getAttribute("src") || "";
        const alt = node.getAttribute("alt") || "image";

        if (src) output.push(`![${alt}](${src})`);
        return;
      }

      if (tag === "li") {
        output.push("\n- ");
        node.childNodes.forEach(walk);
        output.push("\n");
        return;
      }

      const blockTags = new Set([
        "p",
        "div",
        "section",
        "article",
        "main",
        "header",
        "footer",
        "ul",
        "ol",
        "table",
        "tr",
        "td",
        "th",
      ]);

      if (blockTags.has(tag)) output.push("\n");

      node.childNodes.forEach(walk);

      if (blockTags.has(tag)) output.push("\n");
    }

    function domToMarkdownChildren(element) {
      const temporary = document.createElement("div");
      temporary.append(...Array.from(element.childNodes).map((n) => n.cloneNode(true)));
      return domToMarkdown(temporary);
    }

    walk(root);

    return output
      .join("")
      .replace(/[ \t]+\n/g, "\n")
      .replace(/\n{3,}/g, "\n\n")
      .trim();
  }

  function findRoot() {
    const candidates = [
      document.querySelector("main"),
      document.querySelector('[role="main"]'),
      document.querySelector("body"),
    ].filter(Boolean);

    return candidates.find(visible) || document.body;
  }

  function findMessages(root) {
    const all = [];

    for (const selector of MESSAGE_SELECTORS) {
      root.querySelectorAll(selector).forEach((element) => {
        if (!visible(element)) return;

        const text = cleanText(element.innerText || element.textContent);
        if (!text && !element.querySelector("pre, code, img")) return;

        all.push(element);
      });
    }

    const unique = [...new Set(all)];

    // Remove nested message containers.
    const topLevel = unique.filter(
      (element) =>
        !unique.some(
          (other) => other !== element && other.contains(element)
        )
    );

    return topLevel
      .sort((a, b) => {
        const ar = a.getBoundingClientRect();
        const br = b.getBoundingClientRect();
        return ar.top - br.top;
      })
      .filter((element) => {
        const content = domToMarkdown(element);
        return content.length > 0;
      });
  }

  function detectRole(element, index) {
    const attributes = [
      element.getAttribute("data-role"),
      element.getAttribute("data-author"),
      element.getAttribute("aria-label"),
      element.className,
      element.parentElement?.className,
    ]
      .filter(Boolean)
      .join(" ")
      .toLowerCase();

    if (/\b(user|human|you|me)\b/.test(attributes)) return "user";
    if (/\b(assistant|bot|ai|mammouth)\b/.test(attributes)) return "assistant";

    const alignment = getComputedStyle(element).textAlign;
    if (alignment === "right") return "user";

    return index % 2 === 0 ? "user" : "assistant";
  }

  function collectConversation() {
    const root = findRoot();
    const elements = findMessages(root);

    const messages = elements.map((element, index) => ({
      index,
      role: detectRole(element, index),
      content: domToMarkdown(element),
      html: element.innerHTML,
    }));

    return {
      title: document.title || "Mammouth.ai Conversation",
      url: location.href,
      exportedAt: new Date().toISOString(),
      messages,
    };
  }

  function markdownExport(data) {
    let result = `# ${data.title}\n\n`;
    result += `> **Source:** [Mammouth.ai](${data.url})  \n`;
    result += `> **Exported:** ${data.exportedAt}\n\n`;
    result += "---\n\n";

    for (const message of data.messages) {
      result += `## ${message.role === "user" ? "User" : "Assistant"}\n\n`;
      result += `${message.content}\n\n`;
      result += "---\n\n";
    }

    return result.trim() + "\n";
  }

  function textExport(data) {
    let result = `${data.title}\n${"=".repeat(data.title.length)}\n\n`;

    for (const message of data.messages) {
      result += `${message.role.toUpperCase()}\n\n`;
      result += `${message.content}\n\n`;
      result += `${"-".repeat(70)}\n\n`;
    }

    return result;
  }

  function download(content, extension, mime) {
    const safeTitle = (document.title || "mammouth-conversation")
      .replace(/[<>:"/\\|?*\x00-\x1F]/g, "")
      .replace(/\s+/g, "_")
      .slice(0, 100);

    const blob = new Blob([content], {
      type: `${mime};charset=utf-8`,
    });

    const url = URL.createObjectURL(blob);
    const link = document.createElement("a");

    link.href = url;
    link.download = `${safeTitle}.${extension}`;
    document.body.appendChild(link);
    link.click();
    link.remove();

    setTimeout(() => URL.revokeObjectURL(url), 1000);
  }

  function exportConversation(format) {
    const data = collectConversation();

    if (!data.messages.length) {
      alert(
        "No messages were detected. Scroll through the complete conversation first, then try again."
      );
      return;
    }

    if (format === "json") {
      download(
        JSON.stringify(data, null, 2),
        "json",
        "application/json"
      );
    } else if (format === "text") {
      download(textExport(data), "txt", "text/plain");
    } else {
      download(markdownExport(data), "md", "text/markdown");
    }
  }

  function createButton() {
    if (document.getElementById(BUTTON_ID)) return;

    const button = document.createElement("button");
    button.id = BUTTON_ID;
    button.textContent = "⇩ Export";

    Object.assign(button.style, {
      position: "fixed",
      right: "20px",
      bottom: "20px",
      zIndex: "2147483647",
      padding: "11px 15px",
      border: "none",
      borderRadius: "10px",
      background: "#2563eb",
      color: "#fff",
      cursor: "pointer",
      font: "600 14px system-ui, sans-serif",
      boxShadow: "0 4px 14px rgba(0,0,0,.3)",
    });

    button.title =
      "Click: Markdown | Shift-click: JSON | Ctrl/Cmd-click: TXT | Right-click: menu";

    button.addEventListener("click", (event) => {
      if (event.shiftKey) {
        exportConversation("json");
      } else if (event.ctrlKey || event.metaKey) {
        exportConversation("text");
      } else {
        exportConversation("markdown");
      }
    });

    button.addEventListener("contextmenu", (event) => {
      event.preventDefault();

      const choice = prompt(
        "Choose export format:\n\n1 — Markdown\n2 — Plain text\n3 — JSON",
        "1"
      );

      if (choice === "1") exportConversation("markdown");
      if (choice === "2") exportConversation("text");
      if (choice === "3") exportConversation("json");
    });

    document.body.appendChild(button);
  }

  function observePage() {
    new MutationObserver(() => {
      if (!document.getElementById(BUTTON_ID)) {
        createButton();
      }
    }).observe(document.documentElement, {
      childList: true,
      subtree: true,
    });
  }

  async function init() {
    while (!document.body) {
      await sleep(250);
    }

    createButton();
    observePage();
  }

  init();
})();
```
