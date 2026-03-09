<script setup lang="ts">
import { ref } from 'vue'
import { useRouter, useRoute } from 'vue-router'
import { useAuthStore } from '@/stores/auth'
import {
  Sidebar,
  SidebarContent,
  SidebarFooter,
  SidebarGroup,
  SidebarGroupContent,
  SidebarGroupLabel,
  SidebarHeader,
  SidebarMenu,
  SidebarMenuButton,
  SidebarMenuItem,
  SidebarProvider,
  SidebarInset,
  SidebarTrigger,
} from '@/components/ui/sidebar'
import { Separator } from '@/components/ui/separator'
import { Button } from '@/components/ui/button'
import {
  LayoutDashboard,
  ListTodo,
  Users,
  Trophy,
  ScrollText,
  FileSearch,
  Settings,
  LogOut,
} from 'lucide-vue-next'

const router = useRouter()
const route = useRoute()
const auth = useAuthStore()

const showLogoutConfirm = ref(false)

const menuItems = [
  { title: '仪表盘', icon: LayoutDashboard, path: '/dashboard' },
  { title: '任务管理', icon: ListTodo, path: '/tasks' },
  { title: 'Agent', icon: Users, path: '/agents' },
  { title: '积分排行', icon: Trophy, path: '/scores' },
  { title: '活动日志', icon: ScrollText, path: '/logs' },
  { title: '审查记录', icon: FileSearch, path: '/reviews' },
  { title: '系统设置', icon: Settings, path: '/settings' },
]

function handleLogout() {
  auth.logout()
  router.push('/login')
}
</script>

<template>
  <SidebarProvider>
    <Sidebar>
      <SidebarHeader class="p-4">
        <div class="flex items-center gap-3">
          <div
            class="flex h-8 w-8 items-center justify-center rounded-lg bg-primary text-primary-foreground text-sm font-bold">
            M
          </div>
          <div>
            <div class="font-semibold text-sm">OpenMOSS</div>
            <div class="text-xs text-muted-foreground">管理控制台</div>
          </div>
        </div>
      </SidebarHeader>

      <SidebarContent>
        <SidebarGroup>
          <SidebarGroupLabel>导航</SidebarGroupLabel>
          <SidebarGroupContent>
            <SidebarMenu>
              <SidebarMenuItem v-for="item in menuItems" :key="item.path">
                <SidebarMenuButton as-child :is-active="route.path === item.path">
                  <router-link :to="item.path">
                    <component :is="item.icon" />
                    <span>{{ item.title }}</span>
                  </router-link>
                </SidebarMenuButton>
              </SidebarMenuItem>
            </SidebarMenu>
          </SidebarGroupContent>
        </SidebarGroup>
      </SidebarContent>

      <SidebarFooter class="p-4">
        <Button variant="ghost" class="w-full justify-start gap-2" @click="showLogoutConfirm = true">
          <LogOut class="h-4 w-4" />
          退出登录
        </Button>
      </SidebarFooter>
    </Sidebar>

    <SidebarInset>
      <header class="flex h-14 items-center gap-2 border-b px-4">
        <SidebarTrigger />
        <Separator orientation="vertical" class="h-4" />
        <h1 class="text-sm font-medium">
          {{menuItems.find(i => i.path === route.path)?.title || ''}}
        </h1>
      </header>
      <main class="flex-1 p-6">
        <router-view />
      </main>
    </SidebarInset>
  </SidebarProvider>

  <!-- 退出登录确认弹窗 -->
  <Teleport to="body">
    <Transition name="fade">
      <div v-if="showLogoutConfirm" class="fixed inset-0 z-50 flex items-center justify-center">
        <!-- 遮罩 -->
        <div class="absolute inset-0 bg-black/50 backdrop-blur-sm" @click="showLogoutConfirm = false" />
        <!-- 弹窗 -->
        <div
          class="relative z-10 w-full max-w-sm rounded-xl border bg-background p-6 shadow-2xl animate-in fade-in zoom-in-95 duration-200">
          <div class="space-y-2 text-center">
            <div class="mx-auto flex h-12 w-12 items-center justify-center rounded-full bg-muted">
              <LogOut class="h-5 w-5 text-muted-foreground" />
            </div>
            <h2 class="text-lg font-semibold">确认退出</h2>
            <p class="text-sm text-muted-foreground">退出后需要重新输入管理员密码登录</p>
          </div>
          <div class="mt-6 flex gap-3">
            <Button variant="outline" class="flex-1" @click="showLogoutConfirm = false">取消</Button>
            <Button variant="destructive" class="flex-1" @click="handleLogout">确认退出</Button>
          </div>
        </div>
      </div>
    </Transition>
  </Teleport>
</template>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
