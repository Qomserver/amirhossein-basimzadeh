import React, { useEffect, useRef, useState, useCallback, useMemo } from 'react';
import { Page, Listing, User, Post } from '../types';
import { ListingCard } from '../components/ListingCard';
import { ProfessionalCard } from '../components/ProfessionalCard';
import { PostCard } from '../components/social/PostCard';

declare const gsap: any;
declare const ScrollTrigger: any;

interface CounterProps {
  value: number;
  suffix?: string;
  duration?: number;
  delay?: number;
}

const Counter: React.FC<CounterProps> = ({ value, suffix = '', duration = 2.5, delay = 0 }) => {
  const ref = useRef<HTMLSpanElement>(null);
  const [hasAnimated, setHasAnimated] = useState(false);

  useEffect(() => {
    const element = ref.current;
    if (!element || hasAnimated) return;

    const counter = { val: 0 };
    
    const animation = gsap.to(counter, {
      val: value,
      duration: duration,
      delay: delay,
      ease: "power2.out",
      onUpdate: () => {
        if (element) {
          element.textContent = Math.ceil(counter.val).toLocaleString('fa-IR') + suffix;
        }
      },
      onComplete: () => setHasAnimated(true)
    });

    const trigger = ScrollTrigger.create({
      trigger: element,
      start: 'top 85%',
      onEnter: () => {
        animation.play();
      },
      onLeaveBack: () => {
        if (!hasAnimated) {
          animation.restart().pause();
        }
      },
      once: true,
    });
    
    return () => {
      animation.kill();
      trigger.kill();
    };
  }, [value, suffix, duration, delay, hasAnimated]);

  return <span ref={ref} className="inline-block">0{suffix}</span>;
};

interface SectionProps {
  children: React.ReactNode;
  className?: string;
  id?: string;
}

const Section: React.FC<SectionProps> = ({ children, className = '', id }) => (
  <section 
    id={id}
    className={`relative py-16 md:py-24 lg:py-32 overflow-hidden ${className}`}
  >
    {children}
  </section>
);

interface SectionTitleProps {
  children: React.ReactNode;
  subtitle: string;
  className?: string;
}

const SectionTitle: React.FC<SectionTitleProps> = ({ children, subtitle, className = '' }) => (
  <div className={`text-center mb-12 md:mb-16 lg:mb-20 max-w-4xl mx-auto px-4 ${className}`}>
    <h2 className="text-3xl md:text-4xl lg:text-5xl xl:text-6xl font-black text-[var(--color-text-primary)] mb-4 md:mb-6 leading-tight">
      {children}
    </h2>
    <p className="text-base md:text-lg lg:text-xl text-[var(--color-text-secondary)] leading-relaxed max-w-3xl mx-auto">
      {subtitle}
    </p>
  </div>
);

interface LoadingSpinnerProps {
  size?: 'sm' | 'md' | 'lg';
  className?: string;
}

const LoadingSpinner: React.FC<LoadingSpinnerProps> = ({ size = 'md', className = '' }) => {
  const sizeClasses = {
    sm: 'w-4 h-4',
    md: 'w-8 h-8',
    lg: 'w-12 h-12'
  };

  return (
    <div className={`animate-spin rounded-full border-2 border-[var(--color-primary)] border-t-transparent ${sizeClasses[size]} ${className}`} />
  );
};

interface FeatureCardProps {
  icon: string;
  title: string;
  description: string;
  delay?: number;
}

const FeatureCard: React.FC<FeatureCardProps> = ({ icon, title, description, delay = 0 }) => {
  const cardRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    const element = cardRef.current;
    if (!element) return;

    gsap.from(element, {
      opacity: 0,
      y: 50,
      scale: 0.95,
      duration: 0.8,
      delay: delay,
      ease: 'power3.out',
      scrollTrigger: {
        trigger: element,
        start: 'top 90%',
        toggleActions: 'play none none none',
      }
    });
  }, [delay]);

  return (
    <div 
      ref={cardRef}
      className="glass-card group p-6 md:p-8 lg:p-10 rounded-2xl lg:rounded-3xl text-center"
    >
      <div className="relative mb-6 md:mb-8">
        <img 
          src={icon} 
          alt={title}
          className="h-12 w-12 md:h-16 md:w-16 lg:h-20 lg:w-20 mx-auto transition-all duration-500 group-hover:scale-110" 
          loading="lazy"
        />
        <div className="absolute inset-0 bg-gradient-to-r from-[var(--color-primary)] to-[var(--color-secondary)] rounded-full blur-xl opacity-0 group-hover:opacity-20 transition-opacity duration-500" />
      </div>
      <h3 className="text-lg md:text-xl lg:text-2xl font-bold text-[var(--color-text-primary)] mb-3 md:mb-4 group-hover:text-[var(--color-primary)] transition-colors duration-300">
        {title}
      </h3>
      <p className="text-sm md:text-base text-[var(--color-text-secondary)] leading-relaxed group-hover:text-[var(--color-text-primary)] transition-colors duration-300">
        {description}
      </p>
    </div>
  );
};

interface TechItemProps {
  name: string;
  src: string;
  delay?: number;
}

const TechItem: React.FC<TechItemProps> = React.memo(({ name, src, delay = 0 }) => {
  const itemRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    const element = itemRef.current;
    if (!element) return;

    gsap.from(element, {
      opacity: 0,
      scale: 0.8,
      y: 30,
      duration: 0.6,
      delay: delay,
      ease: 'back.out(1.7)',
      scrollTrigger: {
        trigger: element,
        start: 'top 95%',
        toggleActions: 'play none none none',
      }
    });
  }, [delay]);

  return (
    <div 
      ref={itemRef}
      className="flex flex-col items-center gap-2 md:gap-3 group cursor-pointer p-3 md:p-4 rounded-xl transition-all duration-300 hover:bg-black/5"
      title={name}
    >
      <div className="relative">
        <img 
          src={src} 
          alt={name} 
          className="h-12 w-12 md:h-16 md:w-16 lg:h-20 lg:w-20 object-contain transition-all duration-500 group-hover:scale-125" 
          loading="lazy" 
        />
      </div>
      <span className="text-xs md:text-sm text-[var(--color-text-secondary)] group-hover:text-[var(--color-text-primary)] transition-colors duration-300 font-medium">
        {name}
      </span>
    </div>
  );
});

interface StatCardProps {
  value: number;
  suffix: string;
  label: string;
  color: string;
  delay?: number;
}

const StatCard: React.FC<StatCardProps> = ({ value, suffix, label, color, delay = 0 }) => {
  const cardRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    const element = cardRef.current;
    if (!element) return;

    gsap.from(element, {
      opacity: 0,
      y: 60,
      scale: 0.9,
      duration: 1,
      delay: delay,
      ease: 'power3.out',
      scrollTrigger: {
        trigger: element,
        start: 'top 85%',
        toggleActions: 'play none none none',
      }
    });
  }, [delay]);

  return (
    <div 
      ref={cardRef}
      className="glass-card group p-6 md:p-8 lg:p-10 rounded-2xl lg:rounded-3xl text-center"
    >
      <div className={`text-4xl md:text-5xl lg:text-6xl xl:text-7xl font-black mb-3 md:mb-4 leading-none transition-all duration-500 group-hover:scale-110`} style={{ color }}>
        <Counter value={value} suffix={suffix} duration={2.5} delay={delay + 0.3} />
      </div>
      <p className="text-sm md:text-base lg:text-lg text-[var(--color-text-secondary)] font-medium group-hover:text-[var(--color-text-primary)] transition-colors duration-300">
        {label}
      </p>
      <div className="absolute inset-0 rounded-2xl lg:rounded-3xl bg-gradient-to-br opacity-0 group-hover:opacity-5 transition-opacity duration-500" style={{ background: `linear-gradient(135deg, ${color}20, transparent)` }} />
    </div>
  );
};

interface CustomerLogoProps {
  name: string;
  src: string;
  delay?: number;
  title?: string;
}

const CustomerLogo: React.FC<CustomerLogoProps> = React.memo(({ name, src, delay = 0, title }) => {
  const logoRef = useRef<HTMLImageElement>(null);

  useEffect(() => {
    const element = logoRef.current;
    if (!element) return;

    gsap.from(element, {
      opacity: 0,
      scale: 0.8,
      duration: 0.6,
      delay: delay,
      ease: 'power2.out',
      scrollTrigger: {
        trigger: element,
        start: 'top 95%',
        toggleActions: 'play none none none',
      }
    });
  }, [delay]);

  return (
    <img 
      ref={logoRef}
      src={src} 
      alt={name} 
      title={title || name}
      className="h-8 md:h-10 lg:h-12 object-contain grayscale hover:grayscale-0 transition-all duration-500 hover:scale-110 cursor-pointer opacity-70 hover:opacity-100" 
      loading="lazy" 
    />
  );
});

const FaqItem: React.FC<{ question: string; answer: string; isOpen: boolean; onClick: () => void; }> = ({ question, answer, isOpen, onClick }) => {
  const contentRef = useRef<HTMLDivElement>(null);

  return (
    <div className="border-b border-[var(--color-border)]">
      <button
        onClick={onClick}
        className="w-full flex justify-between items-center text-right py-5 px-2 focus:outline-none"
        aria-expanded={isOpen}
      >
        <h3 className="text-lg font-semibold text-[var(--color-text-primary)]">{question}</h3>
        <span className="text-[var(--color-primary)] transition-transform duration-300 transform" style={{ transform: isOpen ? 'rotate(45deg)' : 'rotate(0)' }}>
          <svg className="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M12 4v16m8-8H4" /></svg>
        </span>
      </button>
      <div
        ref={contentRef}
        className="overflow-hidden transition-all duration-500 ease-in-out"
        style={{ maxHeight: isOpen ? `${contentRef.current?.scrollHeight}px` : '0px' }}
      >
        <p className="pb-5 px-2 text-[var(--color-text-secondary)] leading-loose">
          {answer}
        </p>
      </div>
    </div>
  );
};

const FaqSection: React.FC = () => {
  const [openIndex, setOpenIndex] = useState<number | null>(0);

  const faqs = [
    {
      question: 'سرویس Google Center چیست و چگونه کار می‌کند؟',
      answer: 'Google Center یک پلتفرم توسعه نرم‌افزار مبتنی بر هوش مصنوعی است. شما ایده یا نیازمندی خود را به زبان فارسی توصیف می‌کنید و سرویس ما کدهای کامل، بهینه و امن را در تکنولوژی‌های مختلف برای شما تولید می‌کند. این فرآیند شامل تولید کدهای فرانت‌اند، بک‌اند، پایگاه داده و حتی تست‌های خودکار می‌شود.'
    },
    {
      question: 'برای چه تکنولوژی‌هایی می‌توانید کد تولید کنید؟',
      answer: 'ما از طیف گسترده‌ای از تکنولوژی‌های محبوب از جمله React, Next.js, Node.js, Python (Django, Flask), Go, Docker و دیتابیس‌های PostgreSQL و MongoDB پشتیبانی می‌کنیم. لیست تکنولوژی‌ها به طور مداوم در حال گسترش است.'
    },
    {
      question: 'آیا کد تولید شده آماده استفاده در محیط پروداکشن است؟',
      answer: 'بله، کدهای تولید شده با رعایت بهترین استانداردها (Best Practices)، اصول امنیتی و قابلیت مقیاس‌پذیری بالا نوشته می‌شوند. همچنین، مستندات کامل و تست‌های خودکار نیز به همراه کد ارائه می‌شود تا استقرار و نگهداری آن آسان باشد.'
    },
    {
      question: 'هزینه استفاده از سرویس چگونه محاسبه می‌شود؟',
      answer: 'ما پلن‌های متنوعی از جمله پلن رایگان برای شروع، پلن‌های اشتراکی ماهانه برای تیم‌ها و توسعه‌دهندگان حرفه‌ای و پلن سازمانی برای شرکت‌های بزرگ ارائه می‌دهیم. جزئیات کامل قیمت‌ها در صفحه "قیمت‌گذاری" موجود است.'
    },
    {
      question: 'امنیت داده‌ها و کدهای من چگونه تضمین می‌شود؟',
      answer: 'امنیت اطلاعات شما اولویت اصلی ماست. تمام داده‌ها به صورت رمزنگاری شده منتقل و ذخیره می‌شوند. کدهای تولید شده متعلق به شما هستند و ما هیچ‌گونه مالکیتی بر روی آن‌ها نداریم. همچنین، زیرساخت ما از آخرین استانداردهای امنیتی پیروی می‌کند.'
    },
    {
      question: 'آیا امکان سفارشی‌سازی کد تولید شده وجود دارد؟',
      answer: 'قطعاً. شما دسترسی کامل به سورس کد تولید شده دارید و می‌توانید آن را به هر شکلی که نیاز دارید تغییر دهید، توسعه دهید و سفارشی‌سازی کنید. هدف ما سرعت بخشیدن به فرآیند توسعه اولیه شماست.'
    }
  ];

  const handleToggle = (index: number) => {
    setOpenIndex(openIndex === index ? null : index);
  };

  return (
    <div className="max-w-4xl mx-auto">
      {faqs.map((faq, index) => (
        <FaqItem
          key={index}
          question={faq.question}
          answer={faq.answer}
          isOpen={openIndex === index}
          onClick={() => handleToggle(index)}
        />
      ))}
    </div>
  );
};

interface HomePageProps {
  onNavigate: (page: Page, id?: string) => void;
  listings: Listing[];
  professionals: User[];
  posts: Post[];
  currentUser: User | null;
  onToggleSave: (listingId: string) => void;
  onToggleLike: (postId: string) => void;
  isLoading: boolean;
}

export const HomePage: React.FC<HomePageProps> = ({ 
  onNavigate, 
  listings, 
  professionals, 
  posts, 
  currentUser, 
  onToggleSave, 
  onToggleLike,
  isLoading
}) => {
  const [activeSection, setActiveSection] = useState('');
  const [scrollProgress, setScrollProgress] = useState(0);

  const heroRef = useRef<HTMLDivElement>(null);
  const mainRef = useRef<HTMLDivElement>(null);

  const handleNavigation = useCallback((page: Page, id?: string) => {
    try {
      onNavigate(page, id);
    } catch (error) {
      console.error('Navigation error:', error);
    }
  }, [onNavigate]);

  const technologies = useMemo(() => [
    { name: 'React', src: 'https://liara.ir/_next/static/media/react.da024f9b.webp' },
    { name: 'Node.js', src: 'https://liara.ir/_next/static/media/nodejs.fab872cf.webp' },
    { name: 'Python', src: 'https://liara.ir/_next/static/media/python.514d2b54.webp' },
    { name: 'Docker', src: 'https://liara.ir/_next/static/media/docker.b2ec7792.webp' },
    { name: 'PostgreSQL', src: 'https://liara.ir/_next/static/media/postgresql.5390d867.webp' },
    { name: 'MongoDB', src: 'https://liara.ir/_next/static/media/mongodb.6af198d1.webp' },
    { name: 'Next.js', src: 'https://liara.ir/_next/static/media/nextjs.3a462af3.webp' },
    { name: 'Django', src: 'https://liara.ir/_next/static/media/django.48c9f695.webp' },
    { name: 'Golang', src: 'https://liara.ir/_next/static/media/golang.6a9ba5b8.webp' },
    { name: 'PHP', src: 'https://liara.ir/_next/static/media/php.fb6fa8cb.webp' },
    { name: 'DotNet', src: 'https://liara.ir/_next/static/media/dotnet.249831b1.webp' },
    { name: 'Redis', src: 'https://liara.ir/_next/static/media/redis.220c0d5b.webp' },
    { name: 'Vue.js', src: 'https://liara.ir/_next/static/media/vue.5bb3b08e.webp' },
    { name: 'Angular', src: 'https://liara.ir/_next/static/media/angular.2b0c9b5a.webp' },
    { name: 'Laravel', src: 'https://liara.ir/_next/static/media/laravel.c0b2c5f1.webp' },
    { name: 'MySQL', src: 'https://liara.ir/_next/static/media/mysql.8f98f7d2.webp' }
  ], []);
    
  const customers = useMemo(() => [
    { name: 'Snapp', src: 'https://liara.ir/assets/images/customer/snapp.svg' },
    { name: 'Alibaba', src: 'https://liara.ir/assets/images/customer/alibaba.svg' },
    { name: 'Jabama', src: 'https://liara.ir/assets/images/customer/jabama.svg' },
    { name: 'Basalam', src: 'https://liara.ir/assets/images/customer/basalam.svg' },
    { name: 'Technolife', src: 'https://liara.ir/assets/images/customer/technolife.svg' },
    { name: 'Paziresh24', src: 'https://liara.ir/assets/images/customer/paziresh24.svg' },
    { name: 'Digikala', src: 'https://liara.ir/assets/images/customer/digikala.svg' },
    { name: 'Bamilo', src: 'https://liara.ir/assets/images/customer/bamilo.svg' }
  ], []);

  const partners = useMemo(() => [
    { name: 'GGLCT Tech Partner', src: 'https://s3.afranet.net/gapgpt-main-3/user_files/10c5cacd-1f22-4a1d-8056-f7a10eb8bfd9.png', title: 'WordPress, Laravel, PHP Support' },
    { name: 'ParsVDS', src: 'https://storage.gapgpt.app/media/code_interpreter/04e1af6b-a91a-4223-b0a0-8b04bd26cedd/image_gen_ef69eb28-bbbd-4657-a2c3-383c5e65464e_0.png', title: 'پشتیبان هاستینگ' },
    { name: 'AvalAI', src: 'https://storage.gapgpt.app/media/code_interpreter/04e1af6b-a91a-4223-b0a0-8b04bd26cedd/image_gen_f8b577e9-b08a-42c9-9bff-c5d5c79cdcbc_0.png', title: 'پلتفرم و پشتیبان هوش مصنوعی' },
  ], []);

  const features = useMemo(() => [
    { 
      icon: 'https://liara.ir/assets/images/icon-pay-as-you-go/card.svg', 
      title: 'تولید کد Full-Stack', 
      description: 'از ایده تا کد آماده‌ی استقرار در چند ثانیه. فرانت‌اند، بک‌اند و دیتابیس کامل با بهترین استانداردها.' 
    },
    { 
      icon: 'https://liara.ir/assets/images/icon-pay-as-you-go/delivery.svg', 
      title: 'امن و قابل اتکا', 
      description: 'کدهای تولید شده با رعایت بهترین استانداردهای امنیتی، تست خودکار و قابلیت اطمینان ۹۹.۹ درصدی.' 
    },
    { 
      icon: 'https://liara.ir/assets/images/icon-pay-as-you-go/doc.svg', 
      title: 'مستندسازی خودکار', 
      description: 'تولید مستندات کامل و شفاف، API Documentation و راهنمای توسعه برای کدهای شما به صورت خودکار.' 
    },
    { 
      icon: 'https://liara.ir/assets/images/icon-pay-as-you-go/support.svg', 
      title: 'پشتیبانی ۲۴/۷', 
      description: 'تیم پشتیبانی متخصص ما همیشه آماده کمک به شما است. چت آنلاین، تیکت و تماس تلفنی.' 
    },
    { 
      icon: 'https://liara.ir/assets/images/icon-pay-as-you-go/scale.svg', 
      title: 'مقیاس‌پذیری بالا', 
      description: 'زیرساخت قدرتمند ما از هزاران درخواست همزمان پشتیبانی می‌کند و به صورت خودکار scale می‌شود.' 
    },
    { 
      icon: 'https://liara.ir/assets/images/icon-pay-as-you-go/analytics.svg', 
      title: 'تحلیل و آنالیتیکس', 
      description: 'داشبورد پیشرفته برای نظارت بر عملکرد، آمار کاربران و بهینه‌سازی خودکار منابع سیستم.' 
    }
  ], []);

  const stats = useMemo(() => [
    { value: 75000, suffix: '+', label: 'مجموع استقرارها', color: 'var(--color-primary)' },
    { value: 25000, suffix: '+', label: 'توسعه‌دهنده فعال', color: 'var(--color-secondary)' },
    { value: 99, suffix: '%', label: 'آپ‌تایم سرویس‌ها', color: 'var(--color-danger)' },
    { value: 500, suffix: '+', label: 'پروژه موفق', color: 'var(--color-text-primary)' }
  ], []);

  useEffect(() => {
    document.title = 'Google Center - ساخت نرم‌افزار با هوش مصنوعی';
    const metaDescription = document.querySelector('meta[name="description"]');
    if (metaDescription) {
      metaDescription.setAttribute('content', 'با Google Center، نرم‌افزارهای مقیاس‌پذیر و امن بسازید. ابزار هوش مصنوعی برای توسعه ۱۰ برابر سریع‌تر.');
    }
  }, []);

  useEffect(() => {
    if (isLoading) return;

    gsap.registerPlugin(ScrollTrigger);

    const handleScroll = () => {
      const scrolled = window.scrollY;
      const maxScroll = document.documentElement.scrollHeight - window.innerHeight;
      const progress = (scrolled / maxScroll) * 100;
      setScrollProgress(progress);
    };

    window.addEventListener('scroll', handleScroll, { passive: true });

    gsap.utils.toArray('.animated-section').forEach((section: any, index: number) => {
      gsap.from(section, {
        opacity: 0,
        y: 80,
        scale: 0.95,
        duration: 1.2,
        delay: index * 0.1,
        ease: 'power3.out',
        scrollTrigger: {
          trigger: section,
          start: 'top 85%',
          toggleActions: 'play none none none',
          onEnter: () => {
            setActiveSection(section.id || '');
          }
        }
      });
    });

    if (heroRef.current) {
      gsap.from(heroRef.current.querySelectorAll('.hero-element'), {
        opacity: 0,
        y: 100,
        stagger: 0.2,
        duration: 1.5,
        ease: 'power3.out',
        delay: 0.5
      });
    }

    return () => {
      window.removeEventListener('scroll', handleScroll);
      ScrollTrigger.getAll().forEach(trigger => trigger.kill());
    };
  }, [isLoading]);

  if (isLoading) {
    return (
      <div className="min-h-screen bg-white flex items-center justify-center">
        <div className="text-center">
          <LoadingSpinner size="lg" className="mx-auto mb-4" />
          <p className="text-[var(--color-text-primary)] text-lg">در حال بارگذاری...</p>
        </div>
      </div>
    );
  }

  return (
    <>
      <div 
        className="fixed top-0 left-0 w-full h-1 bg-gradient-to-r from-[var(--color-primary)] to-[var(--color-secondary)] z-50 origin-left transition-transform duration-300"
        style={{ transform: `scaleX(${scrollProgress / 100})` }}
      />

      <main ref={mainRef} className="bg-[var(--color-background)] overflow-hidden relative">
        <div className="container mx-auto px-4 sm:px-6 lg:px-8 xl:px-12 relative z-10">
          <Section className="min-h-screen flex items-center justify-center text-center !pt-24 md:!pt-32 lg:!pt-40 !pb-16 md:!pb-24">
            <div ref={heroRef} className="max-w-6xl mx-auto">
              <div className="hero-element mb-6 md:mb-8">
                <span className="inline-block px-4 py-2 bg-gradient-to-r from-[var(--color-primary)] to-orange-400 text-white text-sm md:text-base font-semibold rounded-full mb-6 md:mb-8">
                  🚀 انقلابی در توسعه نرم‌افزار
                </span>
              </div>
              
              <h1 className="hero-element text-4xl md:text-6xl lg:text-7xl xl:text-8xl font-black text-[var(--color-text-primary)] leading-tight md:leading-tight mb-6 md:mb-8">
                با شریک 
                <span className="text-[var(--color-primary)]"> هوش مصنوعی </span>
                خود بسازید، تست کنید و مستقر شوید
              </h1>
              
              <p className="hero-element text-lg md:text-xl lg:text-2xl text-[var(--color-text-secondary)] max-w-4xl mx-auto mb-10 md:mb-12 leading-relaxed">
                Google Center به شما کمک می‌کند تا با سرعت ۱۰ برابر، نرم‌افزارهای مقیاس‌پذیر و امن بسازید. 
                از ایده تا محصول نهایی در کمترین زمان ممکن.
              </p>
              
              <div className="hero-element flex flex-col sm:flex-row gap-4 md:gap-6 justify-center items-center">
                <button 
                  onClick={() => handleNavigation('generator')} 
                  className="w-full sm:w-auto btn-primary text-lg md:text-xl px-8 md:px-12 py-4 md:py-5 font-bold rounded-xl transition-all duration-300 hover:scale-105"
                >
                  🎯 شروع رایگان
                </button>
                <button 
                  onClick={() => handleNavigation('listings')} 
                  className="w-full sm:w-auto bg-transparent border-2 border-[var(--color-text-primary)] text-[var(--color-text-primary)] hover:bg-[var(--color-text-primary)] hover:text-white text-lg md:text-xl px-8 md:px-12 py-4 md:py-5 font-bold rounded-xl transition-all duration-300 hover:scale-105"
                >
                  🎬 مشاهده آگهی‌ها
                </button>
              </div>
              
              <div className="hero-element mt-12 md:mt-16 flex flex-wrap justify-center items-center gap-6 md:gap-8 text-sm md:text-base text-[var(--color-text-secondary)]">
                <div className="flex items-center gap-2">
                  <span className="w-2 h-2 bg-[var(--color-secondary)] rounded-full animate-pulse"></span>
                  بدون نیاز به کارت اعتباری
                </div>
                <div className="flex items-center gap-2">
                  <span className="w-2 h-2 bg-[var(--color-primary)] rounded-full animate-pulse"></span>
                  راه‌اندازی در ۳۰ ثانیه
                </div>
              </div>

              <div className="hero-element mt-16 md:mt-24">
                <img 
                  src="https://s3.afranet.net/gapgpt-main-3/user_files/d9053109-3ba1-4165-aa9b-960af6603e2d.png" 
                  alt="Google Center AI Code Generator Interface" 
                  className="w-full max-w-6xl mx-auto rounded-2xl shadow-2xl border border-[var(--color-border)]"
                  loading="lazy"
                />
              </div>
              
            </div>
          </Section>
          
          <Section id="features" className="animated-section bg-[var(--color-background-light)] rounded-3xl">
            <div className="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-6 md:gap-8 lg:gap-10">
              {features.map((feature, index) => (
                <FeatureCard 
                  key={feature.title}
                  icon={feature.icon}
                  title={feature.title}
                  description={feature.description}
                  delay={index * 0.15}
                />
              ))}
            </div>
          </Section>

          <Section id="listings" className="animated-section">
            <SectionTitle subtitle="برترین متخصصان را پیدا کنید یا پروژه و خدمات خود را در معرض دید هزاران کارفرما قرار دهید.">
              آخرین آگهی‌ها و فرصت‌ها
            </SectionTitle>
            <div className="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-6 md:gap-8">
              {listings.slice(0, 3).map((listing, index) => (
                <div
                  key={listing.id}
                  className="opacity-0 animate-fade-in-up"
                  style={{ animationDelay: `${index * 0.1}s`, animationFillMode: 'forwards' }}
                >
                  <ListingCard 
                    listing={listing} 
                    onClick={() => handleNavigation('listing', listing.id)}
                    onToggleSave={onToggleSave}
                    isSaved={currentUser?.data.savedListings.includes(listing.id) || false}
                  />
                </div>
              ))}
            </div>
            <div className="text-center mt-12 md:mt-16">
              <button 
                onClick={() => handleNavigation('listings')} 
                className="bg-gray-100 text-[var(--color-text-primary)] hover:bg-gray-200 px-8 md:px-10 py-3 md:py-4 text-base md:text-lg font-semibold rounded-xl transition-all duration-300 hover:scale-105"
              >
                مشاهده همه آگهی‌ها ({listings.length}+)
              </button>
            </div>
          </Section>
          
          <Section id="professionals" className="animated-section bg-[var(--color-background-light)] rounded-3xl">
            <SectionTitle subtitle="جامعه‌ای از برترین توسعه‌دهندگان، طراحان و متخصصان هوش مصنوعی، آماده همکاری با شما.">
              متخصصین برتر و تأیید شده
            </SectionTitle>
            <div className="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-6 md:gap-8">
              {professionals.slice(0, 3).map((user, index) => (
                <div
                  key={user.id}
                  className="opacity-0 animate-fade-in-up"
                  style={{ animationDelay: `${index * 0.1}s`, animationFillMode: 'forwards' }}
                >
                  <ProfessionalCard 
                    user={user} 
                    onClick={() => handleNavigation('profile', user.id)}
                  />
                </div>
              ))}
            </div>
            <div className="text-center mt-12 md:mt-16">
              <button 
                onClick={() => handleNavigation('professionals')} 
                className="bg-white border border-[var(--color-border)] text-[var(--color-text-primary)] hover:bg-gray-50 px-8 md:px-10 py-3 md:py-4 text-base md:text-lg font-semibold rounded-xl transition-all duration-300 hover:scale-105"
              >
                مشاهده همه متخصصین ({professionals.length}+)
              </button>
            </div>
          </Section>
          
          {currentUser && posts.length > 0 && (
            <Section id="social-gram" className="animated-section">
              <SectionTitle subtitle="آخرین دستاوردها، پروژه‌ها و تجربیات متخصصان را در شبکه اجتماعی ما دنبال کنید.">
                آخرین پست‌های گوگل سنتر گرام
              </SectionTitle>
              <div className="max-w-5xl mx-auto grid grid-cols-1 lg:grid-cols-2 gap-6 md:gap-8">
                {posts.slice(0, 2).map((post, index) => (
                  <div
                    key={post.id}
                    className="opacity-0 animate-fade-in-up"
                    style={{ animationDelay: `${index * 0.1}s`, animationFillMode: 'forwards' }}
                  >
                    <PostCard
                      post={post}
                      currentUser={currentUser}
                      onToggleLike={onToggleLike}
                      onNavigate={handleNavigation}
                    />
                  </div>
                ))}
              </div>
              <div className="text-center mt-12 md:mt-16">
                <button 
                  onClick={() => handleNavigation('gram')} 
                   className="bg-gray-100 text-[var(--color-text-primary)] hover:bg-gray-200 px-8 md:px-10 py-3 md:py-4 text-base md:text-lg font-semibold rounded-xl transition-all duration-300 hover:scale-105"
                >
                  ورود به گوگل سنتر گرام
                </button>
              </div>
            </Section>
          )}

          <Section id="technologies" className="animated-section bg-[var(--color-background-light)] rounded-3xl">
            <SectionTitle subtitle="ما از جدیدترین و محبوب‌ترین تکنولوژی‌های روز دنیا پشتیبانی می‌کنیم تا شما با خیال راحت بر روی محصول خود تمرکز کنید.">
              تکنولوژی‌های پشتیبانی شده
            </SectionTitle>
            <div className="grid grid-cols-3 md:grid-cols-4 lg:grid-cols-6 xl:grid-cols-8 gap-6 md:gap-8 lg:gap-10">
              {technologies.map((tech, index) => (
                <TechItem
                  key={tech.name}
                  name={tech.name}
                  src={tech.src}
                  delay={index * 0.05}
                />
              ))}
            </div>
          </Section>

          <Section id="stats" className="animated-section">
            <SectionTitle subtitle="آمار و ارقامی که نشان‌دهنده اعتماد هزاران توسعه‌دهنده و شرکت به Google Center است.">
              آمار پلتفرم در یک نگاه
            </SectionTitle>
            <div className="grid grid-cols-2 lg:grid-cols-4 gap-6 md:gap-8">
              {stats.map((stat, index) => (
                <StatCard
                  key={stat.label}
                  value={stat.value}
                  suffix={stat.suffix}
                  label={stat.label}
                  color={stat.color}
                  delay={index * 0.2}
                />
              ))}
            </div>
          </Section>

          <Section id="customers" className="animated-section bg-[var(--color-background-light)] rounded-3xl">
            <SectionTitle subtitle="بزرگترین شرکت‌های ایران برای توسعه و استقرار زیرساخت‌های خود به ما اعتماد کرده‌اند.">
              مورد اعتماد بهترین‌ها
            </SectionTitle>
            <div className="flex flex-wrap justify-center items-center gap-8 md:gap-12 lg:gap-16 xl:gap-20">
              {customers.map((customer, index) => (
                <CustomerLogo
                  key={customer.name}
                  name={customer.name}
                  src={customer.src}
                  delay={index * 0.1}
                />
              ))}
            </div>
          </Section>

          <Section id="partners" className="animated-section">
            <SectionTitle subtitle="ما با بهترین‌ها همکاری می‌کنیم تا زیرساختی قدرتمند و قابل اتکا برای شما فراهم کنیم.">
              همکاران تکنولوژی و زیرساخت
            </SectionTitle>
            <div className="flex flex-wrap justify-center items-center gap-8 md:gap-12 lg:gap-16 xl:gap-20">
              {partners.map((partner, index) => (
                <CustomerLogo
                  key={partner.name}
                  name={partner.name}
                  src={partner.src}
                  title={partner.title}
                  delay={index * 0.1}
                />
              ))}
            </div>
          </Section>

          <Section id="faq" className="animated-section">
            <SectionTitle subtitle="پاسخ به سوالات رایج در مورد سرویس تولید کد با هوش مصنوعی، قیمت‌گذاری و جنبه‌های فنی.">
              سوالات متداول
            </SectionTitle>
            <FaqSection />
          </Section>
          
          <Section id="cta" className="animated-section !pb-32">
            <div className="relative bg-[var(--color-text-primary)] rounded-3xl lg:rounded-[3rem] p-8 md:p-12 lg:p-20 text-center shadow-2xl overflow-hidden">
              <div className="relative z-10">
                <h2 className="text-4xl md:text-5xl lg:text-6xl xl:text-7xl font-black text-white mb-6 md:mb-8 leading-tight" style={{textShadow: '0 2px 4px rgba(0,0,0,0.2)'}}>
                  آماده‌اید تا آینده را بسازید؟
                </h2>
                <p className="text-lg md:text-xl lg:text-2xl text-gray-100 max-w-3xl mx-auto mb-10 md:mb-12 leading-relaxed">
                  به بیش از ۲۵۰۰۰ توسعه‌دهنده بپیوندید و همین امروز اولین پروژه خود را با هوش مصنوعی مستقر کنید.
                </p>
                
                <div className="flex flex-col sm:flex-row gap-4 md:gap-6 justify-center items-center">
                  <button 
                    onClick={() => handleNavigation('generator')} 
                    className="w-full sm:w-auto bg-[var(--color-primary)] text-white font-bold text-lg md:text-xl py-4 md:py-5 px-10 md:px-12 rounded-xl hover:bg-orange-500 transition-all duration-300 transform hover:scale-105 shadow-xl"
                  >
                    🚀 رایگان شروع کنید
                  </button>
                </div>
              </div>
            </div>
          </Section>

        </div>
      </main>
    </>
  );
};
